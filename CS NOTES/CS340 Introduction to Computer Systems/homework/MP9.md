# CS340 mp9

### `mazegen.py`

```python
import asyncio
import aiohttp
from aiohttp import web
from aiohttp.web import RouteTableDef, run_app, Application, Request, Response, json_response

import mazelib  # optional

import random
import logging

# Configure logging
logging.basicConfig(level=logging.ERROR)
logger = logging.getLogger(__name__)

routes = RouteTableDef()

@routes.get('/')
async def index(request: Request) -> Response:
    """Done for you.
    Works with __main__ to display an informative front-end"""
    return Response(text=index_file, content_type="text/html")

@routes.post('/notify')
async def alert(request: Request) -> Response:
    """Done for you.
    If you POST a URL to /notify, this function POSTs this app's URL to the URL it is sent."""
    url = await request.text()
    print('POSTing', whoami, 'to', url)
    async with aiohttp.ClientSession() as session:
        async with session.post(url, data=whoami) as resp:
            return Response(status=resp.status, headers=resp.headers, body=await resp.read())

@routes.get('/static')
async def static_maze(request: Request) -> Response:
    """Returns the same 7x7 maze with 4 exits every time it is called"""

    random.seed(12)  # Ensure reproducibility for the static maze

    maze_vertices_list = [(x, y) for x in range(7) for y in range(7)]
    all_possible_edges = set()

    # Add all possible right and down edges
    for x in range(6):
        for y in range(7):
            all_possible_edges.add(frozenset([(x, y), (x + 1, y)]))

    for x in range(7):
        for y in range(6):
            all_possible_edges.add(frozenset([(x, y), (x, y + 1)]))

    # Construct adjacency list for Wilson's algorithm
    adjacency = {vertex: [] for vertex in maze_vertices_list}
    for edge in all_possible_edges:
        if len(edge) != 2:
            logger.error(f"Invalid edge in all_possible_edges: {edge}")
            continue
        v1, v2 = tuple(edge)
        if v1 == v2:
            logger.error(f"Self-edge detected: {v1}")
            continue
        adjacency[v1].append(v2)
        adjacency[v2].append(v1)

    # Wilson's Algorithm Implementation
    tree = set()
    maze_edges_final = set()

    # Step 1: Initialize all vertices as unused
    unused_vertices = set(maze_vertices_list)

    # Step 2: Choose a random starting vertex and add it to the tree
    start_vertex = random.choice(maze_vertices_list)
    tree.add(start_vertex)
    unused_vertices.remove(start_vertex)

    # Step 3: Repeat until all vertices are in the tree
    while unused_vertices:
        # Pick a random vertex from the unused vertices
        current = random.choice(list(unused_vertices))
        path = []
        visited = {}
        current_vertex = current

        # Perform loop-erased random walk until reaching the tree
        while current_vertex not in tree:
            path.append(current_vertex)
            visited[current_vertex] = len(path) - 1
            neighbors = adjacency[current_vertex]
            if not neighbors:
                logger.error(f"No neighbors found for vertex: {current_vertex}")
                break
            next_vertex = random.choice(neighbors)
            if next_vertex in visited:
                # Loop detected; erase the loop
                loop_start = visited[next_vertex]
                path = path[:loop_start + 1]
                visited = {vertex: idx for idx, vertex in enumerate(path)}
            current_vertex = next_vertex

        # Add the path to the tree
        for i in range(len(path) - 1):
            if path[i] == path[i + 1]:
                logger.error(f"Duplicate consecutive vertices in path: {path[i]}")
                continue
            edge = frozenset([path[i], path[i + 1]])
            if len(edge) != 2:
                logger.error(f"Malformed edge detected: {edge}")
                continue
            if edge not in maze_edges_final:
                maze_edges_final.add(edge)
                tree.add(path[i + 1])
                unused_vertices.discard(path[i + 1])
        tree.update(path)
        unused_vertices.difference_update(path)

    # Define exit edges
    exit_edges = {
        'left': frozenset([(0, 3), (-1, 3)]),
        'down': frozenset([(3, 6), (3, 7)]),
        'right': frozenset([(6, 3), (7, 3)]),
        'up': frozenset([(3, 0), (3, -1)])
    }
    dir_list = ['left', 'down', 'right', 'up']

    for dir in dir_list:
        edge = exit_edges[dir]
        if len(edge) != 2:
            logger.error(f"Malformed exit edge detected: {edge}")
            continue
        maze_edges_final.add(edge)

    # Initialize maze list with all walls present (binary: 1111 -> 15)
    maze_list = [[15 for _ in range(7)] for _ in range(7)]

    # Remove walls based on the maze_edges_final
    for edge in maze_edges_final:
        cells = list(edge)
        if len(cells) != 2:
            logger.error(f"Malformed edge in maze_edges_final: {cells}")
            continue
        cell1, cell2 = cells
        x1, y1 = cell1
        x2, y2 = cell2

        if 0 <= x1 < 7 and 0 <= y1 < 7 and 0 <= x2 < 7 and 0 <= y2 < 7:
            if x2 == x1 + 1:
                maze_list[y1][x1] &= ~4  # Remove right wall of cell1
                maze_list[y2][x2] &= ~1  # Remove left wall of cell2
            elif x2 == x1 - 1:
                maze_list[y1][x1] &= ~1  # Remove left wall of cell1
                maze_list[y2][x2] &= ~4  # Remove right wall of cell2
            elif y2 == y1 + 1:
                maze_list[y1][x1] &= ~2  # Remove bottom wall of cell1
                maze_list[y2][x2] &= ~8  # Remove top wall of cell2
            elif y2 == y1 - 1:
                maze_list[y1][x1] &= ~8  # Remove top wall of cell1
                maze_list[y2][x2] &= ~2  # Remove bottom wall of cell2
        else:
            # Handle exit edges
            if 0 <= x1 < 7 and 0 <= y1 < 7:
                x, y = x1, y1
                outside_cell = (x2, y2)
            else:
                x, y = x2, y2
                outside_cell = (x1, y1)

            if outside_cell[0] == x - 1:
                maze_list[y][x] &= ~1  # Remove left wall
            elif outside_cell[0] == x + 1:
                maze_list[y][x] &= ~4  # Remove right wall
            elif outside_cell[1] == y - 1:
                maze_list[y][x] &= ~8  # Remove top wall
            elif outside_cell[1] == y + 1:
                maze_list[y][x] &= ~2  # Remove bottom wall

    # Serialize the maze for JSON response
    maze_serialized = [''.join(f'{cell:X}' for cell in row) for row in maze_list]

    return json_response(maze_serialized)

@routes.get('/dynamic/{h}')
async def dynamic_maze(request: Request) -> Response:
    """Returns a different 7x7 maze each time it is called,
    with between 0 and 4 exits as specified by the hex digit in the URL:
    /dynamic/0 has 4 exits, /dynamic/1 has 3 (left is closed),
    and so on up to /dynamic/F which has no exits at all."""
    try:
        kind = int(request.match_info['h'], 16)
    except ValueError:
        logger.error(f"Invalid hex value for 'h': {request.match_info['h']}")
        return web.Response(status=400, text="Invalid hex value")

    open_walls = [not (kind & 1), not (kind & 2), not (kind & 4), not (kind & 8)]  # left, down, right, up

    maze_vertices_list = [(x, y) for x in range(7) for y in range(7)]
    all_possible_edges = set()

    # Add all possible right and down edges
    for x in range(6):
        for y in range(7):
            all_possible_edges.add(frozenset([(x, y), (x + 1, y)]))

    for x in range(7):
        for y in range(6):
            all_possible_edges.add(frozenset([(x, y), (x, y + 1)]))

    # Construct adjacency list for Wilson's algorithm
    adjacency = {vertex: [] for vertex in maze_vertices_list}
    for edge in all_possible_edges:
        if len(edge) != 2:
            logger.error(f"Invalid edge in all_possible_edges: {edge}")
            continue
        v1, v2 = tuple(edge)
        if v1 == v2:
            logger.error(f"Self-edge detected: {v1}")
            continue
        adjacency[v1].append(v2)
        adjacency[v2].append(v1)

    # Wilson's Algorithm Implementation
    tree = set()
    maze_edges_final = set()

    # Step 1: Initialize all vertices as unused
    unused_vertices = set(maze_vertices_list)

    # Step 2: Choose a random starting vertex and add it to the tree
    start_vertex = random.choice(maze_vertices_list)
    tree.add(start_vertex)
    unused_vertices.remove(start_vertex)

    # Step 3: Repeat until all vertices are in the tree
    while unused_vertices:
        # Pick a random vertex from the unused vertices
        current = random.choice(list(unused_vertices))
        path = []
        visited = {}
        current_vertex = current

        # Perform loop-erased random walk until reaching the tree
        while current_vertex not in tree:
            path.append(current_vertex)
            visited[current_vertex] = len(path) - 1
            neighbors = adjacency[current_vertex]
            if not neighbors:
                logger.error(f"No neighbors found for vertex: {current_vertex}")
                break
            next_vertex = random.choice(neighbors)
            if next_vertex in visited:
                # Loop detected; erase the loop
                loop_start = visited[next_vertex]
                path = path[:loop_start + 1]
                visited = {vertex: idx for idx, vertex in enumerate(path)}
            current_vertex = next_vertex

        # Add the path to the tree
        for i in range(len(path) - 1):
            if path[i] == path[i + 1]:
                logger.error(f"Duplicate consecutive vertices in path: {path[i]}")
                continue
            edge = frozenset([path[i], path[i + 1]])
            if len(edge) != 2:
                logger.error(f"Malformed edge detected: {edge}")
                continue
            if edge not in maze_edges_final:
                maze_edges_final.add(edge)
                tree.add(path[i + 1])
                unused_vertices.discard(path[i + 1])
        tree.update(path)
        unused_vertices.difference_update(path)

    # Define exit edges
    exit_edges = {
        'left': frozenset([(0, 3), (-1, 3)]),
        'down': frozenset([(3, 6), (3, 7)]),
        'right': frozenset([(6, 3), (7, 3)]),
        'up': frozenset([(3, 0), (3, -1)])
    }
    dir_list = ['left', 'down', 'right', 'up']
    for i, dir in enumerate(dir_list):
        if open_walls[i]:
            edge = exit_edges[dir]
            if len(edge) != 2:
                logger.error(f"Malformed exit edge detected: {edge}")
                continue
            maze_edges_final.add(edge)

    # Initialize maze list with all walls present (binary: 1111 -> 15)
    maze_list = [[15 for _ in range(7)] for _ in range(7)]

    # Remove walls based on the maze_edges_final
    for edge in maze_edges_final:
        cells = list(edge)
        if len(cells) != 2:
            logger.error(f"Malformed edge in maze_edges_final: {cells}")
            continue
        cell1, cell2 = cells
        x1, y1 = cell1
        x2, y2 = cell2

        if 0 <= x1 < 7 and 0 <= y1 < 7 and 0 <= x2 < 7 and 0 <= y2 < 7:
            if x2 == x1 + 1:
                maze_list[y1][x1] &= ~4  # Remove right wall of cell1
                maze_list[y2][x2] &= ~1  # Remove left wall of cell2
            elif x2 == x1 - 1:
                maze_list[y1][x1] &= ~1  # Remove left wall of cell1
                maze_list[y2][x2] &= ~4  # Remove right wall of cell2
            elif y2 == y1 + 1:
                maze_list[y1][x1] &= ~2  # Remove bottom wall of cell1
                maze_list[y2][x2] &= ~8  # Remove top wall of cell2
            elif y2 == y1 - 1:
                maze_list[y1][x1] &= ~8  # Remove top wall of cell1
                maze_list[y2][x2] &= ~2  # Remove bottom wall of cell2
        else:
            # Handle exit edges
            if 0 <= x1 < 7 and 0 <= y1 < 7:
                x, y = x1, y1
                outside_cell = (x2, y2)
            else:
                x, y = x2, y2
                outside_cell = (x1, y1)

            if outside_cell[0] == x - 1:
                maze_list[y][x] &= ~1  # Remove left wall
            elif outside_cell[0] == x + 1:
                maze_list[y][x] &= ~4  # Remove right wall
            elif outside_cell[1] == y - 1:
                maze_list[y][x] &= ~8  # Remove top wall
            elif outside_cell[1] == y + 1:
                maze_list[y][x] &= ~2  # Remove bottom wall

    # Serialize the maze for JSON response
    maze_serialized = [''.join(f'{cell:X}' for cell in row) for row in maze_list]

    return json_response(maze_serialized)

if __name__ == '__main__':
    # done for you

    # run the app with custom host and port
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('--host', type=str, default="0.0.0.0")
    parser.add_argument('-p', '--port', type=int, default=5000)
    args = parser.parse_args()

    # figure out our URL (a "fully qualified domain name" or fqdn), and put it into index.html
    import socket
    whoami = socket.getfqdn()
    if '.' not in whoami:
        whoami = 'localhost'
    whoami += ':' + str(args.port)
    whoami = 'http://' + whoami
    index_file = open('index.html').read().replace('UNKNOWN', whoami)

    print('running as', whoami)
    
    app = Application()
    app.add_routes(routes)
    run_app(app, host=args.host, port=args.port)
```

### `mazeview.py`

```python
"""
This file contains an example maze navigation front-end.
It only uses type-0 tiles (openings on all four walls).
It is not needed for the MP in any way, but may prove interesting.
"""

from aiohttp import web, WSMsgType, ClientSession, client_exceptions
from asyncio import Queue, Event, QueueEmpty
import random
import time

routes = web.RouteTableDef()
tileset = {}
tilequeue = Queue()
servers = set()
index_file = None

async def fromServer(url):
    async with ClientSession() as session:
        async with session.get(url) as resp:
            return await resp.json()

async def getATile():
    try:
        return tilequeue.get_nowait()
    except QueueEmpty:
        opts = list(servers)
        random.shuffle(opts)
        for s in opts:
            try:
                await tilequeue.put(await fromServer(s+'/dynamic/0'))
            except client_exceptions.ClientConnectorError:
                print('Disconnected:',s)
                if s in servers: servers.remove(s)
        try:
            return tilequeue.get_nowait()
        except:
            return ["98a0a8c","1494b04","1430e14","0280820","1e575d5","1e1c575","3a202a6"]

@routes.get('/')
async def ui(request):
    return web.Response(text=index_file, content_type='text/html')


@routes.post('/')
async def addMG(request):
    url = await request.text()
    print('registration attempt for', url)
    try:
        await tilequeue.put(await fromServer(url+'/static'))
        if url in servers:
            return web.Response(text='Duplicate connection ignored')
        servers.add(url)
        return web.Response(text='Connected')
    except BaseException as ex:
        print("ERROR connecting to", url)
        print(ex)

@routes.get('/tile/{tx}/{ty}')
async def tile(request):
    global tileset
    tx = int(request.match_info['tx'])
    ty = int(request.match_info['ty'])
    if (tx,ty) not in tileset:
        evt = Event()
        tileset[(tx,ty)] = evt
        tileset[(tx,ty)] = await getATile()
        evt.set()
    elif isinstance(tileset[(tx,ty)], Event):
        await tileset[(tx,ty)].wait()
    return web.json_response({'tile':[tx,ty],'geom':tileset[(tx,ty)]})

if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('--host', type=str, default="0.0.0.0")
    parser.add_argument('-p','--port', type=int, default=34009)
    args = parser.parse_args()

    import socket
    whoami = socket.getfqdn()
    if '.' not in whoami: whoami = 'localhost'
    whoami += ':'+str(args.port)
    whoami = 'http://' + whoami
    
    index_file = open('mazeview.html').read().replace('UNKNOWN', whoami)
    
    app = web.Application()
    app.add_routes(routes)
    web.run_app(app, host=args.host, port=args.port)
```

### `mazeview.html`

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Maze UI</title>
  <script>
window.addEventListener('load', evt=>{
  window.canvas = document.querySelector('canvas')
  window.canvas.style.position = 'absolute'
  window.canvas.style.left = 0
  window.canvas.style.top = 0
  window.canvas.style.width = '100vw'
  window.canvas.style.height = '100vh'
  resizer(evt)
  reqTile(tile_idx)
  document.body.addEventListener('keydown', keypress)

  const label = document.querySelector('center')
  label.style.position = 'absolute'
  label.style.top = '8'

})
window.addEventListener('resize', resizer)
function resizer(evt) {
  window.canvas.width = window.innerWidth * devicePixelRatio
  window.canvas.height = window.innerHeight * devicePixelRatio
  window.c = window.canvas.getContext('2d')
  window.c.lineCap = 'square'
  window.c.lineWidth = 1/16
  let scale = Math.min(window.canvas.height,window.canvas.width)/21
  window.c.setTransform(scale,0, 0,scale, window.canvas.width/2, window.canvas.height/2)
  redraw()
}
tiles = {"0,0":["988088c","1000004","1000004","0000000","1000004","1000004","3220226"]}
tile_idx = [0,0]
cell_idx = [3,3]
motion = false
eom_time = 0
anim_ratio = 0
scale = 100

function reqTile(tidx) {
  if (String(tidx) in tiles) return
  fetch('tile/'+tidx[0]+'/'+tidx[1])
    .then(res => res.json())
    .then(j => {tiles[String(j.tile)] = j.geom; redraw()})
    .catch(ex => {console.error(ex); tiles[String(tidx)] = ["9a8088c","5b02024","5b49494","0a02020","5b4d1e5","1e571e5","3a282a6"]; redraw()})
}

function drawCell(num,dx,dy) {
  if (!num) return
  let dist = Math.hypot(dx,dy)
  if (dist*7 >= 100) return
  c.strokeStyle = 'hsl(0, 0%, '+(dist*7)+'%)'
  c.beginPath()
  if (num&1) { // west
    c.moveTo((dx-0.5),(dy-0.5))
    c.lineTo((dx-0.5),(dy+0.5))
  }
  if (num&2) { // south
    c.moveTo((dx-0.5),(dy+0.5))
    c.lineTo((dx+0.5),(dy+0.5))
  }
  if (num&4) { // east
    c.moveTo((dx+0.5),(dy-0.5))
    c.lineTo((dx+0.5),(dy+0.5))
  }
  if (num&8) { // north
    c.moveTo((dx-0.5),(dy-0.5))
    c.lineTo((dx+0.5),(dy-0.5))
  }
  c.stroke()
}

function redraw() {
  if (!window.c) return
  c.clearRect(-canvas.width/2,-canvas.height/2,canvas.width,canvas.height)

  let adx = 0, ady = 0
  switch(motion) {
    case 'left': adx = -anim_ratio; break
    case 'right': adx = anim_ratio; break
    case 'up': ady = -anim_ratio; break
    case 'down': ady = anim_ratio; break
  }

  for(let tdx = -3; tdx <=3; tdx+=1) {
    for(let tdy = -3; tdy <=3; tdy+=1) {
      let tidx = String([tile_idx[0]+tdx,tile_idx[1]+tdy])
      if (!(tidx in tiles)) continue
      let tile = tiles[tidx]
      for(let cdx = 0; cdx < 7; cdx+=1) {
        for(let cdy = 0; cdy < 7; cdy+=1) {
          drawCell(
            Number.parseInt(tile[cdy][cdx],16),
            (tdx*7 + cdx - cell_idx[0] - adx),
            (tdy*7 + cdy - cell_idx[1] - ady),
          )
        }
      }
    }
  }
  c.fillStyle = '#E84A27'
  c.beginPath()
  c.ellipse(0,0,0.4,0.4,0,-Math.PI,Math.PI)
  c.fill()
}

function motion_tick(dt) {
  if (!eom_time) {
    eom_time = dt + 500
  }
  anim_ratio = Math.min(1 - (eom_time-dt)/500, 1)
  redraw()
  if (anim_ratio < 1) {
    requestAnimationFrame(motion_tick)
  } else {
    eom_time = 0
    anim_ratio = 0
    switch(motion) {
      case 'left':
        cell_idx[0] -= 1;
        if (cell_idx[0] < 0) { cell_idx[0] += 7; tile_idx[0] -= 1; }
        break;
      case 'right':
        cell_idx[0] += 1;
        if (cell_idx[0] > 6) { cell_idx[0] -= 7; tile_idx[0] += 1; }
        break;
      case 'up':
        cell_idx[1] -= 1;
        if (cell_idx[1] < 0) { cell_idx[1] += 7; tile_idx[1] -= 1; }
        break;
      case 'down':
        cell_idx[1] += 1;
        if (cell_idx[1] > 6) { cell_idx[1] -= 7; tile_idx[1] += 1; }
        break;
    }
    window.motion = false
    if (window.queue) {
      moveSelf(window.queue)
      window.queue = false
    }
  }
}

function moveSelf(dir) {
  if (motion) return
  if (!(String(tile_idx) in tiles)) throw Error("Somehow got off the board")
  let tile = tiles[String(tile_idx)]
  let cell = Number.parseInt(tile[cell_idx[1]][cell_idx[0]], 16)
  switch(dir) {
    case 'left':
      if (cell&1) return // wall
      if (cell_idx[0] == 0) reqTile([tile_idx[0]-1,tile_idx[1]])
    break;
    case 'down':
      if (cell&2) return // wall
      if (cell_idx[1] == 6) reqTile([tile_idx[0],tile_idx[1]+1])
    break;
    case 'right':
      if (cell&4) return // wall
      if (cell_idx[0] == 6) reqTile([tile_idx[0]+1,tile_idx[1]])
    break;
    case 'up':
      if (cell&8) return // wall
      if (cell_idx[1] == 0) reqTile([tile_idx[0],tile_idx[1]-1])
    break;
    default: throw Error("Unknown direction "+JSON.stringify(dir))
  }
  motion = dir
  requestAnimationFrame(motion_tick)
}

function keypress(evt) {
//  if (motion) return // mid-motion
  if (!(String(tile_idx) in tiles)) throw Error("Somehow got off the board")
  let tile = tiles[String(tile_idx)]
  let cell = Number.parseInt(tile[cell_idx[1]][cell_idx[0]], 16)
  let dir = false
  switch(evt.key) {
    case 'ArrowLeft': case 'a': case 'A':
      dir = 'left'
    break;
    case 'ArrowDown': case 's': case 'S':
      dir = 'down'
    break;
    case 'ArrowRight': case 'd': case 'D':
      dir = 'right'
    break;
    case 'ArrowUp': case 'w': case 'W':
      dir = 'up'
    break;
  }
  if (dir) {
    if (window.motion) window.queue = dir
    else moveSelf(dir)
  }
}
  </script>
</head><body style="background:#FFF">
<canvas></canvas>
<center>UNKNOWN</center>
</body></html>
```
