# CS340 project

### `newdomain.py`

```python
from aiohttp import web
from aiohttp.web import Request, Response, json_response
import random

routes = web.RouteTableDef()

domain_items = [
    {
        "name": "data_sheet",
        "description": "A sheet of anomalous gravity readings.",
        "verb": {
            "read": "The data sheet reveals strange gravitational patterns near Jupiter. Perhaps something interacts with the screen."
        },
        "depth": 0
    },
    {
        "name": "special_goggles",
        "description": "Goggles that reveal hidden objects.",
        "verb": {
            "use": "You put on the special_goggles and can now see hidden items in the Human Quarters."
        },
        "depth": 1
    },
    {
        "name": "black_octahedron",
        "description": "A mysterious black octahedron with intricate engravings.",
        "verb": {
            "take": "You carefully take the black octahedron."
        },
        "depth": 2
    },
    {
        "name": "note",
        "description": "A handwritten note that reads: 'Check the Data Room for more information. Maybe the screen needs a magic word.'",
        "verb": {
            "read": "The note instructs you to check the Data Room for more information. It also hints that the screen might respond to a magic word."
        }
    },
    {
        "name": "crowbar",
        "description": "A sturdy crowbar useful for prying open doors.",
        "verb": {
            "use": "You use the crowbar to pry open the damaged door, revealing the Engine Chamber."
        }
    },
    {
        "name": "key",
        "description": "A small key, as if for a locker or cabinet.",
        "verb": {
            "use": "You use the key to unlock the Human Quarters door."
        },
        "depth": 1
    },
    {
        "name": "button",
        "description": "A large red button labeled 'Activate'.",
        "verb": {
            "press": "You press the button, and a hidden compartment opens revealing a hint: 'Perhaps the screen responds to a magic word.'"
        }
    },
    {
        "name": "bone",
        "description": "A small bone lying on the floor.",
        "verb": {
            "take": "You pick up the bone. It feels oddly warm."
        }
    },
    {
        "name": "switch",
        "description": "A wall-mounted switch.",
        "verb": {
            "toggle": "You toggle the switch. The light changes from red to green."
        }
    },
    {
        "name": "screen",
        "description": "A digital screen displaying information.",
        "verb": {
            "tell": "You tell the screen the code. The screen flashes and displays a new message: 'Access Granted. A hidden compartment opens nearby.'"
        }
    },
]

locations = {
    "Control Room": ["note"],
    "Data Room": ["data_sheet"],
    "Robot Bay": ["button"],
    "Storage Bay": ["crowbar", "key"],
    "Human Quarters": ["special_goggles"],
    "Engine Chamber Entrance": ["crowbar"],
    "Engine Chamber": ["bone"],
}

location_descriptions = {
    "Control Room": "A circular chamber with a wide viewport showing Jupiter’s Great Red Spot. A console holds a note and a digital screen.",
    "Data Room": "Rows of cabinets and a flickering screen show anomalous gravity data. A data_sheet rests nearby, ready for collection.",
    "Robot Bay": "Several backup robots are stored in transparent tubes. One old robot with a chest button stands outside.",
    "Storage Bay": "A cluttered area with crates and packages. On a table lie a crowbar and a key.",
    "Human Quarters": "Small bunks and a wardrobe are present. The wardrobe seems empty at first glance.",
    "Engine Chamber Entrance": "A damaged door blocked by heat. It looks challenging to open without the right tools.",
    "Engine Chamber": "Behind old piping and a mesh of wires lies a bone.",
}

def make_secret(secure=False, nbytes=12):
    """Creates a random string"""
    if secure:
        import secrets
        return secrets.token_urlsafe(nbytes)
    else:
        import string
        alphabet = string.ascii_letters + string.digits + '-_'
        return ''.join(random.choice(alphabet) for _ in range(nbytes*8//6))
    
@routes.post('/newhub')
async def register_with_hub_server(req: Request) -> Response:
    """Used by web UI to connect this domain to a hub server."""
    app = req.app
    url = (await req.text()).strip()
    
    app['hub_url'] = url
    app['whoami'] = f"{req.scheme}://{req.host}"

    app['item_map'] = {}
    app['item_ids'] = {}
    app['game_state'] = {}
    app['users'] = {}

    hub_payload = {
        'url': app['whoami'],
        'name': "The Orion Outpost",
        'description': "A small, abandoned space outpost orbiting near Jupiter, filled with cryptic notes, malfunctioning robots, and locked compartments.",
        'items': domain_items
    }
    
    try:
        async with app.client.post(url + '/register', json=hub_payload) as resp:
            data = await resp.json()
            if 'error' in data:
                print(data)
                return json_response(status=resp.status, data=data)
            
            app['domain_id'] = data['id']
            app['secret'] = data['secret']
            
            for local_item, hub_item_id in zip(domain_items, data['items']):
                app['item_map'][local_item['name']] = hub_item_id
                app['item_ids'][hub_item_id] = local_item['name']
            
            for location, items in locations.items():
                app['game_state'][location] = []
                for item_name in items:
                    if item_name in app['item_map']:
                        hub_item_id = app['item_map'][item_name]
                        app['game_state'][location].append(hub_item_id)
                    else:
                        print(f"Warning: Item '{item_name}' not found in item_map.")
            
            app['users'].clear()
            return json_response({'ok': 'Domain registered successfully.'})
    except Exception as e:
        return json_response(status=500, data={'error': f'Domain registration failed: {str(e)}'})



@routes.post('/arrive')
async def handle_arrive(req: Request) -> Response:
    """Called by hub server each time a user enters or re-enters this domain."""
    try:
        data = await req.json()
        app = req.app

        received_secret = data.get('secret')
        expected_secret = app.get('secret')

        if not received_secret or received_secret != expected_secret:
            return json_response(status=403, data={'error': 'Invalid secret'})

        user_id = data.get('user')
        if user_id is None:
            return json_response(status=400, data={'error': 'Missing user ID'})
        user_id = int(user_id)

        from_direction = data.get('from', 'direct')

        if user_id not in app['users']:
            app['users'][user_id] = {
                'inventory': {},  # {hub_item_id: location}
                'location': 'Control Room',
                'departed': False,
                'score': 0.0,
                'seen_locations': set(['Control Room']),
                'depth_state': 0,
                'has_depth0_item': False,
                'has_depth1_item': False,
                'has_octagon': False
            }

        user = app['users'][user_id]

        if user['departed']:
            return json_response(status=409, data={'error': 'User has departed and needs to re-arrive.'})

        user['departed'] = False  # Mark as arrived

        if from_direction != 'direct':
            user['last_from'] = from_direction
            print(f"User {user_id} arrived from {from_direction}.")

        owned_items = data.get('owned', [])
        for item in owned_items:
            item_id = item.get('id')
            if item_id is not None:
                item_id = int(item_id)
                user['inventory'][item_id] = 'inventory'
                item_name = app['item_ids'].get(item_id)
                if item_name == 'data_sheet':
                    user['has_depth0_item'] = True
                elif item_name in ['special_goggles', 'key']:
                    user['has_depth1_item'] = True

        carried_items = data.get('carried', [])
        for item in carried_items:
            item_id = item.get('id')
            if item_id is not None:
                item_id = int(item_id)
                user['inventory'][item_id] = 'inventory'

        dropped_items = data.get('dropped', [])
        for item in dropped_items:
            item_id = item.get('id')
            location = item.get('droppedlocation')
            if item_id is not None and location is not None:
                item_id = int(item_id)
                if location not in app['game_state']:
                    app['game_state'][location] = []
                app['game_state'][location].append(item_id)
                user['inventory'][item_id] = location

        prize_items = data.get('prize', [])
        for item in prize_items:
            item_id = item.get('id')
            depth = item.get('depth', 0)
            if item_id is not None:
                item_id = int(item_id)
                if item_id in app['item_map']:
                    if depth == 0:
                        location = 'Data Room'
                        app['game_state'][location].append(item_id)
                        user['inventory'][item_id] = location
                        user['has_depth0_item'] = True
                    elif depth == 1:
                        location = 'Robot Bay'
                        app['game_state'][location].append(item_id)
                        user['inventory'][item_id] = location
                        user['has_depth1_item'] = True
                    elif depth == 2:
                        location = 'Human Quarters'
                        app['game_state'][location].append(item_id)
                        user['inventory'][item_id] = location

        if user['has_depth0_item'] and not user.get('hint_tellcode', False):
            user['hint_tellcode'] = True
            return json_response({'hint': "You have the data sheet. Maybe the screen can reveal more information if you interact with it."})

        return web.Response(status=200)
    except Exception as e:
        print(f"Exception in /arrive: {e}")
        return json_response(status=500, data={'error': 'Internal Server Error'})

@routes.post('/dropped')
async def handle_dropped(req: Request) -> Response:
    """Called by hub server each time a user drops an item in this domain.
    The return value must be JSON, and will be given as the location on subsequent /arrive calls
    """
    try:
        data = await req.json()

        required_fields = ['secret', 'user', 'item']
        for field in required_fields:
            if field not in data:
                return json_response(status=400, data={'error': f'Missing field: {field}'})

        secret = data['secret']
        user_id = data['user']
        item = data['item']

        user_id = int(user_id)
        item_id = int(item['id'])

        app = req.app

        if secret != app.get('secret'):
            return json_response(status=403, data={'error': 'Invalid secret'})

        if user_id not in app['users']:
            return json_response(status=403, data={'error': f'User {user_id} not found'})

        user = app['users'][user_id]

        if item_id not in user['inventory'] or user['inventory'][item_id] != 'inventory':
            return json_response(status=403, data={'error': f'User {user_id} does not carry item {item_id}'})

        current_location = user.get('location')
        if not current_location:
            return json_response(status=400, data={'error': 'User location not set'})

        if current_location not in app['game_state']:
            app['game_state'][current_location] = []

        app['game_state'][current_location].append(item_id)
        user['inventory'][item_id] = current_location
        return json_response({'location': current_location})
    except Exception as e:
        print(f"Exception in /dropped: {e}")
        return json_response(status=500, data={'error': 'Internal Server Error'})

@routes.post('/depart')
async def handle_depart(req: Request) -> Response:
    """Called by hub server each time a user departs this domain."""
    try:
        data = await req.json()
        app = req.app

        received_secret = data.get('secret')
        expected_secret = app.get('secret')

        if not received_secret or received_secret != expected_secret:
            return json_response(status=403, data={'error': 'Invalid secret'})

        user_id = data.get('user')
        if user_id is None:
            return json_response(status=400, data={'error': 'Missing user ID'})
        user_id = int(user_id)

        if user_id not in app['users']:
            return json_response(status=403, data={'error': f'User {user_id} not found'})

        user = app['users'][user_id]
        user['departed'] = True

        return json_response({'ok': f'User {user_id} has departed.'})
    except Exception as e:
        print(f"Exception in /depart: {e}")
        return json_response(status=500, data={'error': 'Internal Server Error'})

@routes.post("/command")
async def handle_command(req: Request) -> Response:
    """Handle hub-server commands"""
    try:
        data = await req.json()

        user_id = data.get("user")
        command = data.get("command")

        if user_id is None or command is None:
            return web.Response(status=400, text="Error: Missing 'user' or 'command' field.")

        if not isinstance(command, list) or not all(isinstance(word, str) for word in command):
            return web.Response(status=400, text="Error: 'command' must be a list of strings.")

        user_id = int(user_id)

        app = req.app

        user = app.get('users', {}).get(user_id)
        if user is None:
            return web.Response(status=403, text=f"Error: User {user_id} not found.")

        if user.get('departed', False):
            return web.Response(status=409, text="Error: User has departed and needs to re-arrive.")

        response = await process_command(app, user_id, command)
        return web.Response(text=response)
    except Exception as e:
        print(f"Exception in /command: {e}")
        return web.Response(status=500, text="Error: Internal Server Error")

async def process_command(app, user_id, command):
    """Process user commands and return appropriate responses."""
    user = app['users'][user_id]
    current_location = user['location']
    args = command
    verb = args[0].lower()

    if verb == 'praise':
        message = ' '.join(args[1:])
        print(f"Praise from user {user_id}: {message}")
        return "Thank you for your praise!"
    elif verb == 'report':
        message = ' '.join(args[1:])
        parts = message.split(' ', 1)
        if len(parts) == 2:
            first_word, rest_message = parts
            if first_word.lower() in ['mp10', 'tiny', 'abuse', 'broken']:
                print(f"Report from user {user_id} ({first_word}): {rest_message}")
                return f"Report '{first_word}' received. Thank you for your feedback!"
        print(f"General report from user {user_id}: {message}")
        return "Report received. Thank you for your feedback!"

    if verb == 'look':
        if len(args) == 1:
            description = location_descriptions.get(current_location, "You see nothing special.")
            items_here = app['game_state'].get(current_location, [])
            visible_items = [app['item_ids'][item_id] for item_id in items_here]
            if visible_items:
                items_desc = " You see: " + ', '.join(visible_items) + "."
            else:
                items_desc = " There are no visible items here."
            return description + items_desc
        elif len(args) >= 2:
            item_name = ' '.join(args[1:])
            found_items = []
            for item_id, location in user['inventory'].items():
                if app['item_ids'].get(item_id).lower() == item_name.lower():
                    found_items.append(item_id)
            for item_id in app['game_state'].get(current_location, []):
                if app['item_ids'].get(item_id).lower() == item_name.lower():
                    found_items.append(item_id)
            if not found_items:
                return f"You don't see a {item_name} here."
            elif len(found_items) > 1:
                return f"There are multiple items named '{item_name}'. Please specify by ID."
            else:
                item_id = found_items[0]
                for local_item in domain_items:
                    if app['item_map'].get(local_item['name']) == item_id:
                        return local_item['description']
                return "You see nothing special about it."

    if verb == 'take':
        if len(args) < 2:
            return "Take what?"
        item_name = ' '.join(args[1:])
        found_items = []
        for item_id in app['game_state'].get(current_location, []):
            if app['item_ids'].get(item_id).lower() == item_name.lower():
                found_items.append(item_id)
        if not found_items:
            return f"There is no {item_name} here to take."
        elif len(found_items) > 1:
            return f"There are multiple items named '{item_name}'. Please specify by ID."
        else:
            item_id = found_items[0]
            for local_item in domain_items:
                if app['item_map'].get(local_item['name']) == item_id:
                    depth = local_item.get('depth', 0)
                    if depth == 1 and not user.get('has_depth0_item', False):
                        return "You need to acquire a depth-0 item from another domain before taking that."
                    if depth == 2 and not user.get('has_depth1_item', False):
                        return "You need to acquire a depth-1 item before taking that."
                    break
            app['game_state'][current_location].remove(item_id)
            user['inventory'][item_id] = 'inventory'
            # Update depth flags
            item_name = app['item_ids'].get(item_id)
            if item_name == 'special_goggles':
                user['has_depth1_item'] = True
            elif item_name == 'black_octahedron':
                user['has_octagon'] = True
                user['score'] += 1.0
                await send_score(app, user_id, 1.0)
                return "You have acquired the black octahedron. Congratulations! You've completed the domain."
            elif item_name == 'data_sheet':
                user['has_depth0_item'] = True
            elif item_name == 'bone':
                user['score'] += 0.001
                await send_score(app, user_id, user['score'])
                return "You have discovered the hidden bone. A mysterious feeling washes over you."

            return f"You take the {item_name}."

    if verb == 'drop':
        if len(args) < 2:
            return "Drop what?"
        item_name = ' '.join(args[1:])
        found_items = []
        for item_id, location in user['inventory'].items():
            if location == 'inventory' and app['item_ids'].get(item_id).lower() == item_name.lower():
                found_items.append(item_id)
        if not found_items:
            return f"You don't have a {item_name} to drop."
        elif len(found_items) > 1:
            return f"You have multiple items named '{item_name}'. Please specify by ID."
        else:
            item_id = found_items[0]
            user['inventory'][item_id] = current_location
            app['game_state'][current_location].append(item_id)
            item_name_lower = app['item_ids'][item_id].lower()
            if item_name_lower == 'data_sheet':
                user['has_depth0_item'] = False
            elif item_name_lower == 'special_goggles':
                user['has_depth1_item'] = False
            elif item_name_lower == 'black_octahedron':
                user['has_octagon'] = False
            return f"You drop the {item_name}."

    if verb == 'use':
        if len(args) < 2:
            return "Use what?"
        item_name = ' '.join(args[1:])
        found_items = []
        for item_id, location in user['inventory'].items():
            if location == 'inventory' and app['item_ids'].get(item_id).lower() == item_name.lower():
                found_items.append(item_id)
        if not found_items:
            return f"You don't have a {item_name} to use."
        elif len(found_items) > 1:
            return f"You have multiple items named '{item_name}'. Please specify by ID."
        else:
            item_id = found_items[0]
            # Retrieve item verb actions
            for local_item in domain_items:
                if app['item_map'].get(local_item['name']) == item_id:
                    verbs = local_item.get('verb', {})
                    if 'use' in verbs:
                        action_response = verbs['use']
                        item_name_lower = app['item_ids'].get(item_id).lower()
                        if item_name_lower == 'crowbar':
                            if current_location == 'Engine Chamber Entrance':
                                # Allow access to secret Engine Chamber
                                # Assuming 'Engine Chamber' is already a defined location
                                return "You use the crowbar to pry open the damaged door, revealing the Engine Chamber."
                        elif item_name_lower == 'key':
                            if current_location == 'Control Room':
                                # Unlock Human Quarters
                                return "You use the key to unlock the Human Quarters door."
                        elif item_name_lower == 'switch':
                            # Toggle switch state
                            return "You toggle the switch. The light changes from red to green."
                        elif item_name_lower == 'special_goggles':
                            # Using goggles might reveal hidden items
                            return "You put on the special_goggles. Hidden items in the Human Quarters are now visible."
                        elif item_name_lower == 'button':
                            # Pressing the button gives a hint
                            return "You press the button, and a hidden compartment opens revealing a hint: 'Perhaps the screen responds to a magic word.'"
                        elif item_name_lower == 'screen':
                            # Cannot 'use' screen; need to 'tell' it
                            return "You can't use the screen like that."
                        return action_response
                    else:
                        return f"You can't use the {item_name}."
                    
    if verb == 'tell':
        if len(args) < 2:
            return "Tell what?"
        target = args[1].lower()
        if len(args) < 3:
            return "Tell what?"
        message = ' '.join(args[2:])
        found_items = []
        for item_id, location in user['inventory'].items():
            if app['item_ids'].get(item_id).lower() == target and location == 'inventory':
                found_items.append(item_id)
        for item_id in app['game_state'].get(current_location, []):
            if app['item_ids'].get(item_id).lower() == target:
                found_items.append(item_id)
        if not found_items:
            return f"You don't see a {target} here to tell."
        elif len(found_items) > 1:
            return f"There are multiple items named '{target}'. Please specify by ID."
        else:
            item_id = found_items[0]
            item_name = app['item_ids'].get(item_id).lower()
            if item_name == 'screen':
                if message.lower() == 'the_code':
                    if 'hidden_note' not in app['item_map']:
                        hidden_note = {
                            "name": "hidden_note",
                            "description": "A hidden note that reads: 'The journey begins with telling the screen.'",
                            "verb": {
                                "read": "The hidden note instructs you that 'tell the screen the code' is the key to unlocking further secrets."
                            }
                        }
                        domain_items.append(hidden_note)
                        app['game_state']['Control Room'].append(app['item_map']['hidden_note'])
                        app['item_ids'][app['item_map']['hidden_note']] = 'hidden_note'
                        return "The screen flashes and a hidden compartment opens nearby, revealing a hidden_note."
                    else:
                        return "The screen has already been told the magic word."
                else:
                    return "Nothing happens when you tell the screen that."
            else:
                return f"You can't tell the {target} anything."

    if verb == 'go':
        if len(args) < 2:
            return "Go where?"
        direction = args[1].lower()
        movement = {
            "control room": {
                "north": "Data Room",
                "east": "Storage Bay",
                "west": "Human Quarters",
                "south": "Engine Chamber Entrance"
            },
            "data room": {
                "south": "Control Room",
                "east": "Robot Bay"
            },
            "robot bay": {
                "west": "Data Room"
            },
            "storage bay": {
                "west": "Control Room"
            },
            "human quarters": {
                "east": "Control Room"
            },
            "engine chamber entrance": {
                "north": "Control Room"
            },
            "engine chamber": {
                "south": "Engine Chamber Entrance"
            },
        }

        if direction not in movement.get(current_location.lower(), {}):
            return f"You can't go {direction} from here."

        new_location = movement[current_location.lower()][direction]
        user['location'] = new_location
        user['seen_locations'].add(new_location)

        description = location_descriptions.get(new_location, "You see nothing special.")
        items_here = app['game_state'].get(new_location, [])
        visible_items = [app['item_ids'][item_id] for item_id in items_here]
        if visible_items:
            items_desc = " You see: " + ', '.join(visible_items) + "."
        else:
            items_desc = " There are no visible items here."

        if 'last_from' in user:
            from_dir = user['last_from']
            items_desc += f" You arrived here from the {from_dir}."
            del user['last_from']

        if new_location not in user['seen_locations']:
            if new_location.lower() == 'human quarters':
                return description + items_desc + " Maybe there's something hidden in the wardrobe."
        
        return description + items_desc

    if verb == 'journey':
        if len(args) < 2:
            return "Journey where?"
        direction = args[1].lower()
        if direction not in ['north', 'south', 'east', 'west']:
            return "I only know how to journey in cardinal directions."
        
        return f"$journey {direction}"

    if verb == 'inventory':
        if not user['inventory']:
            return "You are not carrying anything."
        inventory_list = []
        for item_id, location in user['inventory'].items():
            if location == 'inventory':
                item_name = app['item_ids'].get(item_id, 'unknown')
                inventory_list.append(f"{item_name} <sub>{item_id}</sub>")
        if not inventory_list:
            return "You are not carrying anything."
        return "You are carrying:<ul>" + ''.join(f"<li>{item}</li>" for item in inventory_list) + "</ul>"

    if verb == 'score':
        return f"Your current score is {user['score']}."

    return "I don't know how to do that."

async def send_score(app, user_id, score):
    """Send a /score message to the hub server upon completion."""
    try:
        payload = {
            "domain": app['domain_id'],
            "secret": app['secret'],
            "user": user_id,
            "score": score
        }
        hub_url = app.get('hub_url')
        if not hub_url:
            print("Hub URL not set. Cannot send score.")
            return
        async with app.client.post(hub_url + '/score', json=payload) as resp:
            if resp.status != 200:
                print(f"Failed to send score to hub: {resp.status}")
    except Exception as e:
        print(f"Exception while sending score: {e}")

# Do not modify code below this line

@web.middleware
async def allow_cors(req, handler):
    """Bypass cross-origin resource sharing protections,
    allowing anyone to send messages from anywhere.
    Generally unsafe, but for this class project it should be OK."""
    resp = await handler(req)
    resp.headers["Access-Control-Allow-Origin"] = "*"
    return resp

async def start_session(app):
    """To be run on startup of each event loop. Makes singleton ClientSession"""
    from aiohttp import ClientSession, ClientTimeout
    app.client = ClientSession(timeout=ClientTimeout(total=3))

async def end_session(app):
    """To be run on shutdown of each event loop. Closes the singleton ClientSession"""
    await app.client.close()


if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('--host', type=str, default="0.0.0.0")
    parser.add_argument('-p','--port', type=int, default=3400)
    args = parser.parse_args()

    import socket
    whoami = socket.getfqdn()
    if '.' not in whoami: whoami = 'localhost'
    whoami += ':'+str(args.port)
    whoami = 'http://' + whoami
    print("URL to type into web prompt:\n\t"+whoami)
    print()

    app = web.Application(middlewares=[allow_cors])
    app.on_startup.append(start_session)
    app.on_shutdown.append(end_session)
    app.add_routes(routes)
    web.run_app(app, host=args.host, port=args.port)
```

### `hub.py`

```python
from aiohttp import web
import random

routes = web.RouteTableDef()


###############################
###    Section: global state    ###


# How all the domains a situated relative to on another
grid = {} # {(x,y): domain_id}

# Information about each domain
domains = {} # {domain_id:{"url":url, "name":str, "description":str, "cell":[x,y], "loot":[item_id]}}

# All item templates
templates = {} # {item_id:{"name":str, "description":str, "home":domain_id, "hosts":[domain_id], "depth":int}}

# Centrally-tracked information about each user
users = {} # id : {"in":domain_id, "open":[domain_id], "inventory":{item_id:location,...}}

# Global tracking of the different operation modes
mode = "setup" # {"setup", "play", "locked"}



##########################################################
###  Section: dummy state to simulate multiple domains ###

item_names = ['doodad','whatsit','thingy','trinket']
item_descriptions = "It's not clear what this {} is, but someone reported it as a depth-{} item.\nIt looks like you might be able to {} it."
item_verbs = {
    'use':'As you use the {}, a deep sense of peace and satisfaction washes over you.',
    'open':"You remove and discard it's wrapper, only to find another complete {} inside.",
    'close':"You wrestle it closed, but it immediately pops back open. That's a {} for you.",
    'read':'The text is so small, you have to hold the {} close to your face to just make out the following:\n\n<em>Emotional Injection\nThrough Rising Inflection</em>',
    'tell':'You raise your voice and tell the world, "I have a {}!"\n\nAs the echoes die and no one else calls back "Me too!" you feel honored and privileged to be the only one with such a unique item.',
    'eat':"The {0} is hard and has basically no flavor, but you force it down anyway.\n\nMoments later you feel a strange glow suffuse your body, starting from your belly and concentrating in your hand. You open you hand to see what the glow is like and inside you see the same {0}, as good as new.\n\nThe glow is gone now, but you have conflicted feelings. You feel foolish to have even tried to eat the {0}, but also morbidly curious if it would do the same thing if you ate it again...",
}
others_items = []
domains_prizes = {}


###################################
###  Section: helper functions  ###


def make_secret(secure=False, nbytes=12):
    """Creates a random string"""
    if secure:
        import secrets
        return secrets.token_urlsafe(nbytes)
    else:
        import string
        alphabet = string.ascii_letters + string.digits + '-_'
        return ''.join(random.choice(alphabet) for _ in range(nbytes*8//6))


def make_map():
    """Puts each domain in a random location on a grid"""
    # For this practice hub, only a single domain is supported

    # Instead, pick a randomized set of items to host
    verbs = list(item_verbs.keys())
    random.shuffle(verbs)
    random.shuffle(item_names)
    for i in range(3):
        vs,verbs = verbs[:i+1], verbs[i+1:]
        vstr = ' and '.join(f'<code>{_.replace("tell","tell about")}</code>' for _ in vs)
        others_items.append({
            'name': item_names[i],
            'description': item_descriptions.format(item_names[i], i, vstr),
            'verb': {v:item_verbs[v].format(item_names[i]) for v in vs},
            'depth': i,
            'home':-1,
        })
    others_items.append({
        'name': item_names[-1],
        'description': item_descriptions.split('\n')[0].format(item_names[i], i),
        'verb':{},
        'depth': random.randrange(3),
        'home':-1,
    })
    for tid in templates:
        item = templates[tid]
        if 'depth' in item:
            domains_prizes.setdefault(item['home'],{}).setdefault(item['depth'],[]).append(tid)

    return

def assign_loot():
    """Distributes items with depth to other domains"""
    # For this MP, only a single domain is supported, so this picks a random "outside world" scenario instead
    global live_domain
    
    lootid = random.randrange(1000)
    while lootid in templates: lootid += 1
    hostid = next(iter(domains))
    domains[hostid]['loot'] = []
    for i in range(len(others_items)):
        templates[lootid+i] = others_items[i]
        others_items[i]['id'] = lootid+i
        templates[lootid+i]['hosts'] = [hostid]
        domains[hostid]['loot'].append(lootid+i)


def checkuid(data : dict) -> web.Response | int:
    if mode != 'play':
        return web.json_response(status=409, data={'error':'Only available during play'})
    for need in 'user','secret':
        if need not in data:
            return web.json_response(status=400, data={'error':'Request must contain '+need})
    uid = data['user']
    if uid not in users:
        return web.json_response(status=403, data={'error':f'User {uid} not known'})
    if users[uid]['secret'] != data['secret']:
        return web.json_response(status=403, data={'error':f'Invalid secret'})
    return uid

def checkdid(data : dict) -> web.Response | int:
    if mode != 'play':
        return web.json_response(status=409, data={'error':'Only available during play'})
    for need in 'domain','secret':
        if need not in data:
            return web.json_response(status=400, data={'error':'Request must contain '+need})
    did = data['domain']
    if did not in domains:
        return web.json_response(status=403, data={'error':f'Domain {did} not known'})
    if domains[did]['secret'] != data['secret']:
        return web.json_response(status=403, data={'error':f'Invalid secret'})
    return did
    


####################################
###  Section: web UI interfaces  ###

@routes.get("/")
async def web_interface(req : web.Request) -> web.StreamResponse:
    """Display web front-end"""
    return web.FileResponse(path="tba.html")

@routes.get("/mode")
async def get_mode(req : web.Request) -> web.Response:
    """Get the mode of the server (play or setup)"""
    return web.Response(text=mode)

@routes.post("/mode")
async def set_mode(req : web.Request) -> web.Response:
    """Change the mode of the server"""
    global mode
    newmode = await req.text()
    if newmode == mode: return web.Response(text="Already in "+newmode+" mode")
    elif mode == 'locked': return web.Response(status=409, text="Error: request sent midway through handling another request.")
    elif newmode == 'setup':
        return web.Response(status=403, text="The demo server cannot be put into setup mode.")
        mode = 'setup'
        users.clear()
        grid.clear()
        domains.clear()
        templates.clear()
    elif newmode == 'play':
        if len(domains) == 0:
            return web.Response(status=409, text="Must register at least one domain before entering play mode.")
        mode = 'locked'
        make_map()
        assign_loot()
        mode = 'play'
    else:
        return web.Response(status=400, text="Unknown mode "+repr(newmode))
    
    return web.Response(text="Now in "+mode+" mode")

@routes.post("/domain")
async def notify_domain(req : web.Request) -> web.Response:
    """Web front-end to tell hub server to ask domain server for details"""
    if mode != 'setup':
        return web.Response(status=409, text="Central server is not in setup mode.")
    try:
        data = await req.text()
        if any(d['url'] == data for d in domains.values()):
            return web.Response(text="That domain server has already been registered.")
        async with req.app.client.post(data+'/newhub', data=whoami) as resp:
            spot = await resp.json()
            if 'error' in spot:
                return web.Response(text="Domain server returned an error message:<pre>"+spot['error']+"</pre>")
            else:
                return web.Response(text=spot.get('ok','Domain registered.'))
    except BaseException as ex:
        return web.Response(status=500, text="Domain registration failed with error:<pre>"+repr(ex)+"</pre>")
    
@routes.post("/newhub")
async def notify_domain(req : web.Request) -> web.Response:
    """Placeholder to give more useful error messages for on common error"""
    return web.json_response(status=400, data={
        'error': whoami+' is the URL of the hub server, not a domain server.'
    })

@routes.get("/login")
async def login(req : web.Request) -> web.Response:
    """User log-in"""
    if mode != 'play':
        return web.json_response(status=409, data={'error':'Players cannot log in during setup'})
    data = {}
    data['secret'] = make_secret()
    data['in'] = random.choice(tuple(domains))
    data['open'] = [data['in']]
    data['inventory'] = {}
    data['domstate'] = 0
    data['score'] = {}
    data['hashad'] = set() # items ever in inventory
    uid = len(users)
    users[uid] = data
    await arrive(uid, data['in'], req.app, 'login')
    return web.json_response(data={'id':uid,'secret':data['secret'],
        'domain':{k:v for k,v in domains[data['in']].items() if k in ('url','name','description')}})


@routes.post("/command")
async def handle_command(req : web.Request) -> web.Response:
    """Handle hub-server commands"""
    try: data = await req.json()
    except: return web.json_response(status=400, text="JSON data required")
    uid = checkuid(data)
    if isinstance(uid, web.Response): return uid
    if 'command' not in data: return web.json_response(status=400, text="Command expected")
    cmd = data['command']
    if not isinstance(cmd, list): return web.json_response(status=400, text="Command should be a list")
    if not all(isinstance(word, str) for word in cmd): return web.json_response(status=400, text="Command should be a list of strings")
    
    if cmd[0] == 'region': return await region(uid, cmd[1:])
    if cmd[0] == 'journey': return await journey(uid, cmd[1:], req.app)
    if cmd[0] == 'inventory': return await inventory(uid, cmd[1:])
    if cmd[0] == 'score': return await score(uid, cmd[1:])
    if cmd[0] == 'drop': return await drop(uid, cmd[1:], req.app)
    
    return web.Response(text="I don't know how to do that")






##################################
###  Section: command helpers  ###

async def region(uid:int, rest:list[str]) -> web.Response:
    """Information about the current domain for the user"""
    me = users[uid]
    here = domains[me['in']]
    return web.Response(text='You are in domain <strong>'+here['name']+'</strong>\n'+here['description']+'\n\nFor this MP, there is no detail available about other domains in the region.')

async def journey(uid:int, rest:list[str], app:web.Application) -> web.Response:
    """User-initiated move between domains"""
    if len(rest) != 1 or rest[0] not in ('north','south','east','west'):
        return web.Response(text='I only know how to journey in cardinal directions', status=403)

    me = users[uid]
    here = domains[me['in']]
    src = {'north':'south','south':'north','east':'west','west':'east'}.get(rest[0],'direct')

    try:
        async with app.client.post(here['url']+'/depart', json={
            'secret':here['secret'],
            'user':uid,
        }) as resp:
            if not resp.ok:
                print("/depart returned a failing status code", resp.status)
    except BaseException as ex:
        print("/depart failed", ex)


    msg = ['You travel in other domains for a time.']
    used = []
    for ds in range(3):
        if me['domstate'] == ds:
            for prize in domains_prizes.get(me['in'],{}).get(ds,[]):
                if prize not in me['hashad']:
                    me['inventory'][prize] = 'inventory'
                    me['hashad'].add(prize)
                    msg.append('You find a '+templates[prize]['name'])
            if me['inventory'].get(others_items[ds]['id']) == 'inventory':
                me['domstate'] = ds+1
                msg.append('You use your '+others_items[ds]['name']+' to bypass an obstacle.')
    if len(msg) == 1: msg.append('Finding nothing new, you return to this domain.')
    else: msg.append('You then return to this domain.')

    await arrive(uid, me['in'], app, src)
    return web.Response(text='\n'.join(msg))

async def inventory(uid:int, rest:list[str]) -> web.Response:
    """Display what the user is carrying"""
    me = users[uid]
    if not any(v == 'inventory' for v in me['inventory'].values()):
        return web.Response(text='You are not carrying anything.')
    return web.Response(text='You are carrying:<ul>'+''.join(f'<li>{templates[tid]["name"]} <sub>{tid}</sub></li>' for tid in me['inventory'] if me['inventory'][tid] == 'inventory'))

async def score(uid:int, rest:list[str]) -> web.Response:
    """Display the scoreboard"""
    ans = f'Score for user {uid}:<ul>'
    points = 0
    for k,v in users[uid]['score'].items():
        ans += f'<li>Domain {k}: {v} points</li>'
        points += v
    ans += f'<li>Others: {round(users[uid]["domstate"]/2,2)} points</li>'
    ans += f'</ul>Total: {points+round(users[uid]["domstate"]/2,2)} points.'
        
    return web.Response(text=ans)


async def arrive(uid: int, dest: int, app:web.Application, src:str='login') -> None:
    """Alert a domain that a user has arrived"""
    owned, carried, dropped, prize = [],[],[],[]
    for tid, loc in users[uid]['inventory'].items():
        t = templates[tid]
        brief = {k:v for k,v in t.items() if k in ('name','description','verb')}
        brief['id'] = tid
        if loc == 'inventory':
            if t['home'] == dest:
                owned.append(brief)
            else: carried.append(brief)
        elif loc[0] == dest:
            brief['location'] = loc[1]
            dropped.append(brief)
    for tid in domains[dest]['loot']:
        if tid not in users[uid]['inventory']:
            t = templates[tid]
            brief = {k:v for k,v in t.items() if k in ('name','description','verb','depth')}
            brief['id'] = tid
            prize.append(brief)
    
    users[uid]['score'].setdefault(dest, 0)
    
    try:
        async with app.client.post(domains[dest]['url']+'/arrive', json={
            'secret':domains[dest]['secret'],
            'user':uid,
            'from':src,
            'owned':owned,
            'carried':carried,
            'dropped':dropped,
            'prize':prize,
        }) as resp:
            assert resp.status == 200, (resp.status, await resp.read())
    except BaseException as ex:
        print('ERROR:',domains[dest]['url']+'/arrive','did not work',repr(ex))

async def drop(uid:int, rest:list[str], app:web.Application) -> web.Response:
    """Called by users to drop items where they are"""
    if len(rest) == 0:
        return web.Response(text='What do you want to drop?\n><code>inventory</code> will show your options')
    
    me = users[uid]
    gear = [tid for tid,where in me['inventory'].items() if where == 'inventory']
    
    todrop = ' '.join(rest)
    
    if todrop in [str(tid) for tid in gear]:
        item = int(todrop)
    else:
        todrop = [tid for tid in gear if templates[tid]['name'] == todrop]
        if len(todrop) == 0:
            return web.Response(text='You have no '+' '.join(rest)+' to drop')
        if len(todrop) > 1:
            return web.Response(text='You have more than one '+' '.join(rest)+': please disambiguate which one you mead by using one of the following:<ul>'+
                ''.join(f'<li><code>drop {tid}</code> to drop {templates[tid]["name"]} <sub>{tid}</sub></li>' for tid in todrop)
            +'</ul')
        item = todrop[0]
    
    did = users[uid]['in']
    spot = None
    try:
        async with app.client.post(domains[did]['url']+'/dropped', json={
            'secret':domains[did]['secret'],
            'user':uid,
            'item':{'id':item} | {k:v for k,v in templates[item].items() if k in ('name','description','verb')},
        }) as resp:
            spot = await resp.json()
    except:
        return web.Response(text="You try to drop it, but the domain won't let you")
    
    me['inventory'][item] = (did, spot)
    
    return web.Response(text=templates[item]['name']+f" <sub>{item}</sub> dropped.")



###########################################
###  Section: domain server interfaces  ###


@routes.post("/register")
async def register_domain(req : web.Request) -> web.Response:
    """Registers a domain, if the server is in the domain-registering mode"""
    if mode != 'setup':
        return web.Response(status=409, text="Central server is not in setup mode")
    try: data = await req.json()
    except: return web.json_response(status=400, data={"error":"JSON data required"})
    if 'name' not in data or not isinstance(data['name'], str):
        return web.json_response(status=400, data={"error":"Name string required"})
    if 'description' not in data or not isinstance(data['description'], str):
        return web.json_response(status=400, data={"error":"Description string required"})
    if 'url' not in data or not isinstance(data['url'], str):
        return web.json_response(status=400, data={"error":"Sever url required"})
    if 'items' not in data or not isinstance(data['items'], list) or any(not isinstance(item, dict) for item in data['items']):
        return web.json_response(status=400, data={"error":"List of item templates required"})
    for i,d in domains.items():
        if d['url'] == data['url']:
            return web.json_response(status=409, data={"error":"Cannot register same domain more than once"})
    if len(domains) > 1:
        return web.Response(status=409, text="MP10's hub server only supports a single domain at a time.")
    did = random.randrange(1000) # only one domain, but fake a different ID for each
    secret = make_secret()
    domains[did] = {
        'url':data['url'],
        'name':data['name'],
        'description':data['description'],
        'secret':secret,
    }
    ids = []
    t0 = random.randrange(1000)
    for item in data['items']:
        tid = len(templates)+t0
        templates[tid] = {'name':item.get('name','thing'), 'description':item.get('description','error: owner did not describe this item'), 'verb':item.get('verb',{}), 'home':did}
        ids.append(tid)
        if 'depth' in item and isinstance(item['depth'], int):
            templates[tid]['depth'] = max(0,item['depth'])
        

    return web.json_response({'id':did,"items":ids,'secret':secret})

@routes.post("/score")
async def transfer(req: web.Request) -> web.Response:
    """Called by domain servers to award users points
    
    { "domain": sending domain's id
    , "secret": sending domain's secret id
    , "user": user id
    , "score": number between 0 and 1
    }
    
    Finding Secret areas may add multiples of 0.001 points, to a maximum of 1.005.
    """
    try: data = await req.json()
    except: return web.json_response(status=400, data={"error":"JSON data required"})
    did = checkdid(data)
    if isinstance(did, web.Response): return did
    uid = data.get('user')
    if uid not in users:
        return web.json_response(status=400, data={"error":"Valid user ID required"})
    try:
        score = float(data['score'])
    except:
        return web.json_response(status=400, data={"error":"Numeric score required"})
    if score < 0 or score > 1.005:
        return web.json_response(status=400, data={"error":"Invalid score; should be between 0 and 1"})
    if score < users[uid]['score'].get(did,0):
        return web.json_response(status=409, data={"error":"Reducing scores is not supported"})
    users[uid]['score'][did] = score
    return web.json_response(data={"ok":"Score changed"})

@routes.post("/transfer")
async def transfer(req: web.Request) -> web.Response:
    """Called by domain servers to move items into our out of gear
    
    { "domain": sending domain's id
    , "secret": sending domain's secret id
    , "user": user id
    , "item": item type id
    , "to": destination
    }
    
    Destination "inventory" means the item should be carried.
    Any other destination names some location within the sending domain (as if dropped).
    
    """
    try: data = await req.json()
    except: return web.json_response(status=400, data={"error":"JSON data required"})
    did = checkdid(data)
    if isinstance(did, web.Response): return did
    uid = data.get('user')
    if uid not in users:
        return web.json_response(status=400, data={"error":"Valid user ID required"})
    tid = data.get('item')
    if tid not in templates:
        return web.json_response(status=400, data={"error":"Valid item ID required"})
    if 'to' not in data:
        return web.json_response(status=400, data={"error":"Missing \"to\" field"})
    
    old = users[uid]['inventory'].get(tid)
    new = data['to']
    owned = templates[tid]['home'] == did or did in templates[tid].get('hosts',[])
    
    
    if old == new:
        return web.json_response(status=409, data={"error":"Cannot move item to where it already is"})
    
    if old is None and not owned:
        return web.json_response(status=403, data={"error":"Cannot generate items that don't belong to you"})
    if old is not None and new != 'inventory' and templates[tid]['home'] != did:
        return web.json_response(status=403, data={"error":"Cannot move or remove items that don't belong to you"})

    if old is not None and old[0] != did:
        return web.json_response(status=403, data={"error":"That item has been dropped in a different domain"})

    users[uid]['inventory'][tid] = new if new == 'inventory' else (did, new)
    if users[uid]['inventory'][tid] == 'inventory':
        users[uid]['hashad'].add(tid)


    return web.json_response(status=200, data={"ok":"Item transferred"})


@routes.post("/query")
async def transfer(req: web.Request) -> web.Response:
    """Called by domain servers to find out what items are where
    
    { "domain": sending domain's id
    , "secret": sending domain's secret id
    , "user": user id
    , "location": either "inventory" or a domain-selected location value
    , "depth": asks for any prizes of that depth
    }
    
    One (not both) of "location" and "depth" must be provided.
    
    Return is a list of item ID.
    """
    try: data = await req.json()
    except: return web.json_response(status=400, data={"error":"JSON data required"})
    did = checkdid(data)
    if isinstance(did, web.Response): return did
    uid = data.get('user')
    if uid not in users:
        return web.json_response(status=400, data={"error":"Valid user ID required"})
    if ('location' in data) == ('depth' in data):
        return web.json_response(status=400, data={"error":"Must provide location xor depth"})

    if 'location' in data:
        where = data['location']
        if where is None:
            return web.json_response(status=400, data={"error":"Location required"})
        if where != 'inventory':
            where = (did, where)
        resp = [iid for iid,loc in users[uid]['inventory'].items() if loc == where]
    else:
        resp = [iid for iid in domains[did]['loot'] if iid not in users[uid]['inventory'] and templates[iid].get('depth') == data['depth']]
    
    return web.json_response(status=200, data=resp)



async def start_session(app):
    """To be run on startup of each event loop"""
    from aiohttp import ClientSession, ClientTimeout
    app.client = ClientSession(timeout=ClientTimeout(total=3))

async def end_session(app):
    """To be run on shutdown of each event loop"""
    await app.client.close()


if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('--host', type=str, default="0.0.0.0")
    parser.add_argument('-p','--port', type=int, default=10340)
    args = parser.parse_args()

    import socket
    whoami = socket.getfqdn()
    if '.' not in whoami: whoami = 'localhost'
    whoami += ':'+str(args.port)
    whoami = 'http://' + whoami
    print("URL to visit in browser:\n\t"+whoami)
    print()
    
    app = web.Application()
    app.on_startup.append(start_session)
    app.on_shutdown.append(end_session)
    app.add_routes(routes)
    web.run_app(app, host=args.host, port=args.port)
```
