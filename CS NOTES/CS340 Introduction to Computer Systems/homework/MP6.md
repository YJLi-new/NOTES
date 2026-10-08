# CS340 mp6

### `wallet.py`

```python
import threading

class Wallet:

    def __init__(self):
        """Initialize an empty wallet."""
        self.resources = {}
        self.lock = threading.RLock()
        self.condition = threading.Condition(self.lock)

    def get(self, resource):
        """Returns the amount of a given `resource` in this wallet."""
        with self.lock:
            return self.resources.get(resource, 0)

    def change(self, resource, delta):
        """
        Modifies the amount of a given `resource` in this wallet by `delta`.
        - If `delta` is negative, this function MUST NOT RETURN until the resource can be satisfied.
            (This function MUST BLOCK until the wallet has enough resources to satisfy the request.)
        - Returns the amount of resources in the wallet AFTER the change has been applied.
        """
        with self.lock:
            while True:
                res = self.try_change(resource, delta)
                
                if res is not False:
                    return res
                
                else:
                    self.condition.wait()

    def try_change(self, resource, delta):
        """
        Like change, but if change would block
        this method instead leaves the resource unchanged and returns False.
        """
        with self.lock:
            count = self.resources.get(resource, 0) + delta

            if count >= 0:
                self.resources[resource] = count
                self.condition.notify_all()
                return count
            
            else:
                return False

    def transaction(self, **delta):
        """
        Like calling change(key, value) for each key:value in `delta`, except:
        - All changes are made at once. If any change would block, the entire transaction blocks.
            Only continues once *all* the changes can be made as one atomic action.
        - Returns a dict of {resource:new_value} for all resources in the transaction.
        """
        with self.lock:
            while True:
                is_able_to_proceed = True
                temp_dict = {}

                for resource, change in delta.items():
                    count = self.resources.get(resource, 0) + change
                    if count < 0:
                        is_able_to_proceed = False
                        break
                    temp_dict[resource] = count

                if is_able_to_proceed:
                    for resource, count in temp_dict.items():
                        self.resources[resource] = count
                    self.condition.notify_all()

                    res = {}
                    for resource in delta:
                        res[resource] = self.resources[resource]
                    return res
                
                else:
                    self.condition.wait()
```

### `wallet-server.py`

```python
import threading
import socket
from wallet import Wallet

wallet = Wallet() # the only global variable you should use

def handle(sock):
    try:
        with sock:
            file = sock.makefile('r')
            for line in file:
                line = line.replace('\r', '').strip()
                if not line:
                    continue
                
                parts = line.split()
                if not parts:
                    res = ''
                else:
                    command = parts[0]
                    res = None

                    if command == 'GET' and len(parts) == 2:
                        resource = parts[1]
                        res = wallet.get(resource)

                    elif command == 'MOD' and len(parts) == 3:
                        resource = parts[1]
                        try:
                            delta = int(parts[2])
                            res = wallet.change(resource, delta)
                        except ValueError:
                            res = 'Error: Delta is notan integer'

                    elif command == 'TRY' and len(parts) == 3:
                        resource = parts[1]
                        try:
                            delta = int(parts[2])
                            res = wallet.try_change(resource, delta)
                        except ValueError:
                            res = 'Error: Delta is notan integer'

                    elif (command == 'TRAN') and (len(parts) >= 3) and (len(parts) % 2 == 1):
                        try:
                            delta_args = dict(zip(parts[1::2], map(int, parts[2::2])))
                            res = wallet.transaction(**delta_args)
                        except ValueError:
                            res = 'Error: Delta is notan integer'

                    elif command == 'EXIT':
                        break

                    else:
                        res = 'Error: Undefined command'

                    if command != 'EXIT':
                        sock.sendall((str(res) + '\n').encode('utf-8'))
    except Exception:
        pass


def create_wallet_server(local_port):
    ... # implement this function
    # You will need to use threads, which requires adding additional functions or classes.
    # Make to properly close the listening socket even if an exception is raised, for example by using a finally block
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    try:
        server_socket.bind(('localhost', local_port))
        server_socket.listen(100)
        print(f"Wallet server is listening on port {local_port}...")

        while True:
            client_socket, _ = server_socket.accept()
            threading.Thread(target=handle, args=(client_socket,)).start()
    finally:
        server_socket.close()

if __name__ == '__main__':
    # parses command-line arguments, ensuring all implementations are invoked the same way
    import getopt
    import sys

    local_port = 34000
    optlist, args = getopt.getopt(sys.argv[1:], 'p:')
    for arg in optlist:
        if arg[0] == '-p': local_port = int(arg[1])
    print("Launching wallet server on :"+str(local_port))
    create_wallet_server(local_port)
```

### `gacha.py`

```python
import sys
import threading
from wallet import Wallet
import time
import random

def generate_primogen(wallet):
  total = 0
  while total < 14400:
    r = random.uniform(0,1)
    if r < 0.25: amount = 10
    elif r < 0.75: amount = 20
    else: amount = 50
    wallet.change('primogen', amount)
    total += amount
    sys.stderr.write('✨×{} '.format(amount))
    time.sleep(0.000001)
    

def fate(wallet):
  for i in range(90):
    wallet.change('primogen',-160)
    wallet.change('fate',+1)
    sys.stderr.write('💫 ')

def wish(wallet):
  for i in range(90):
    wallet.transaction(fate=-1, wish=+1)
    sys.stderr.write('🌠 ')

pity_4s = 0
pity_5s = 0

def gacha(wallet):
  global pity_4s, pity_5s
  for i in range(90):
    wallet.change('wish',-1)
    
    r = random.uniform(0,1)
    if r < 0.006: result = 5
    elif r < 0.057: result = 4
    else: result = 3
    
    if result < 4 and pity_4s >= 9: result = 4
    if result < 5 and pity_5s >= 89: result = 5
    
    if result != 4: pity_4s += 1
    if result != 5: pity_5s += 1
    
    if result == 5:
      pity_5s = 0
      sys.stderr.write('🌟🌟🌟🌟🌟\n')
      wallet.change('5*',1)
    elif result == 4:
      pity_4s = 0
      sys.stderr.write('⭐⭐⭐⭐\n')
      wallet.change('4*',1)
    else:
      sys.stderr.write('★★★\n')
      wallet.change('3*',1)
    
  

if __name__ == '__main__':
  print("gacha simulation")
  wallet = Wallet()
  t1 = threading.Thread(target=generate_primogen, args=[wallet])
  t2 = threading.Thread(target=fate, args=[wallet])
  t3 = threading.Thread(target=wish, args=[wallet])
  t4 = threading.Thread(target=gacha, args=[wallet])
  for t in t1,t2,t3,t4: t.start()
  for t in t1,t2,t3,t4: t.join()
  print(file=sys.stderr)
  five = wallet.get('5*')
  print('🌟🌟🌟🌟🌟 found:', five)
  print('⭐⭐⭐⭐ found:', wallet.get('4*'))
  print('★★★ found:', wallet.get('3*'))
  print()
  if five == 0: print('!!: Yikes, your wallet must not be correct.')
  elif five == 1: print('OK, and average luck.')
  elif five == 2: print('WOW, you had exceptional luck.')
  elif five == 3: print('AMAZING, the luck to get this is INSANE!')
  else: print('...I think your wallet is broken. ☹')
```

### `ping-pong.py`

```python
import sys
import threading
from wallet import Wallet

def ping(wallet):
  for i in range(3000):
    wallet.change('ping',+1)
    wallet.change('pong',-1)
    sys.stderr.write('↑')

def pong(wallet):
  for i in range(3000):
    wallet.change('ping',-1)
    wallet.change('pong',+1)
    sys.stderr.write('↓')

if __name__ == '__main__':
  print("ping-pong");
  wallet = Wallet()
  t1 = threading.Thread(target=ping, args=[wallet])
  t2 = threading.Thread(target=pong, args=[wallet])
  t1.start()
  t2.start()
  t1.join()
  t2.join()
  print(file=sys.stderr)
```

### `ping-pong-transaction.py`

```python
import sys
import threading
import random
from wallet import Wallet

def ping(wallet):
  for i in range(3000):
    wallet.transaction(ping=+1, pong=-1)
    sys.stderr.write('↑')

def pong(wallet):
  for i in range(3000):
    wallet.transaction(ping=-1, pong=+1)
    sys.stderr.write('↓')

if __name__ == '__main__':
  print("ping-pong with transactions");
  wallet = Wallet()
  if random.randrange(2) == 0:
    wallet.change('pong', +1)
    print('Should start with ↑')
  else:
    wallet.change('ping', +1)
    print('Should start with ↓')
  t1 = threading.Thread(target=ping, args=[wallet])
  t2 = threading.Thread(target=pong, args=[wallet])
  t1.start()
  t2.start()
  t1.join()
  t2.join()
  print(file=sys.stderr)
```

### `hedgehog-rat.py`

```python
import sys
import threading
from wallet import Wallet
import time

def test_add_worm(wallet):
  for i in range(500):
    wallet.change('worm',+1)
    sys.stderr.write('🐛')
    time.sleep(0.000001)
    
def test_add_corn(wallet):
  for i in range(500):
    wallet.change('corn',+1)
    sys.stderr.write('🌽')
    time.sleep(0.000001)
    


def test_add_hedghog(wallet):
  for i in range(100):
    wallet.change('worm',-3)
    wallet.change('hedgehog',+1)
    sys.stderr.write('🦔')
    time.sleep(0.0001)

def test_add_rat(wallet):
  for i in range(100):
    if wallet.try_change('worm',-3) is False:
      wallet.change('corn',-3)
      sys.stderr.write('🐁')
    else:
      sys.stderr.write('🐀')
    wallet.change('rat',+1)
    time.sleep(0.0002)


if __name__ == '__main__':
  print("Running Test: Test hedgehogs and rats");
  wallet = Wallet()
  ts = [threading.Thread(target=f, args=[wallet]) for f in [test_add_worm, test_add_corn, test_add_hedghog, test_add_rat]]
  for t in ts: t.start()
  for t in ts: t.join()
  print(file=sys.stderr)
  print('Remaining worms 🐛:', wallet.get('worm'))
  print('Remaining corn 🌽:', wallet.get('corn'))
  print('Hedgehogs 🦔:', wallet.get('hedgehog'))
  print('Rats 🐀:', wallet.get('rat'))
```

### `hedgehog-simple.py`

```python
import sys
import threading
from wallet import Wallet
import time
import random

def test_add_hedghog_food(wallet):
  for i in range(500):
    wallet.change('hedgehog-food',+1)
    sys.stderr.write('🐛')
    if random.randrange(6) == 0: time.sleep(0.000001)
    

def test_add_hedghog(wallet):
  for i in range(100):
    wallet.change('hedgehog-food',-3)
    wallet.change('hedgehog',+1)
    sys.stderr.write('🦔')

if __name__ == '__main__':
  print("Running Test: Test hedgehog food and hedgehog");
  wallet = Wallet()
  t1 = threading.Thread(target=test_add_hedghog, args=[wallet])
  t2 = threading.Thread(target=test_add_hedghog_food, args=[wallet])
  t1.start()
  t2.start()
  t1.join()
  t2.join()
  print(file=sys.stderr)
  print('Remaining Hedgehog Food 🐛:', wallet.get('hedgehog-food'))
  print('Hedgehogs 🦔:', wallet.get('hedgehog'))
```

### `degree.py`

```python
import sys
import threading
from wallet import Wallet
import time
import random





def job_clover_patch(wallet):
  for i in range(100):
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("four_leaf_clover", 1); sys.stderr.write("🍀");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    if random.randrange(10) == 0:
      wallet.change("four_leaf_clover", 1); sys.stderr.write("🍀");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    wallet.change("clover", 1); sys.stderr.write("☘️");
    time.sleep(random.uniform(0,0.0001))


def job_orchard(wallet):
  for i in range(110):
    wallet.change("green_apple", 1);
    sys.stderr.write("🍏");
    time.sleep(random.uniform(0,0.0001))


def job_workshop(wallet):
  for i in range(520):
    wallet.change("tools", 1);
    sys.stderr.write("🧰");

    if (i % 5 == 0):
      wallet.change("gem", 1);
      sys.stderr.write("💎");
    time.sleep(random.uniform(0,0.0001))


def job_dna(wallet):
  for i in range(1750):
    wallet.change("dna", 1);
    sys.stderr.write("🧬");
    time.sleep(random.uniform(0,0.0001))


def job_research_green(wallet):
  # 📗 requires 1x🍏 1x🍀 10x☘️ 5x🧬
  for i in range(100):
    wallet.transaction(green_apple=-1, four_leaf_clover=-1, clover=-10, dna=-5, green_book=1);
    sys.stderr.write("📗");
    time.sleep(random.uniform(0,0.0001))

def job_research_blue(wallet):
  # 📘 requires 10x🧬 1x💎
  for i in range(100):
    wallet.transaction(dna=-10, gem=-1, blue_book=1);
    sys.stderr.write("📘");
    time.sleep(random.uniform(0,0.0001))

def job_research_orange(wallet):
  # 📙 requires 5x🧰 2x🧬
  for i in range(100):
    wallet.change("tools", -5);
    wallet.change("dna", -2);
    wallet.change("orange_book", 1);
    sys.stderr.write("📙");
    time.sleep(random.uniform(0,0.0001))

def job_combine_research(wallet):
  # 📚 requires 1x📗, 1x📘, 1x📙
  for i in range(100):
    wallet.change("orange_book", -1);
    wallet.change("blue_book", -1);
    wallet.change("green_book", -1);
    wallet.change("books", 1);
    sys.stderr.write("📚");
    time.sleep(random.uniform(0,0.0001))

def job_graduation(wallet):
  # 🎓 requires 100x 📚 
  wallet.transaction(books=-100, degree=1);
  sys.stderr.write("🎓");
  time.sleep(random.uniform(0,0.0001))



if __name__ == '__main__':
  sys.stderr.write("Resources generated: ");
  wallet = Wallet()
  tids = [threading.Thread(target=globals()[n], args=[wallet]) for n in dir() if n.startswith('job_')]
  for t in tids: t.start()
  for t in tids: t.join()
  print(file=sys.stderr)

  if wallet.get('degree') == 1:
    print("Your wallet contains a degree! 🎓")
    print("- Extra 🧬:", wallet.get('dna'))
    print("- Extra 🍀:", wallet.get('four_leaf_clover'))
  else:
    print("Yikes -- your wallet may not have that degree yet... ☹")
```
