# CS340 mp8

### `classweather.py`

```python
import asyncio
import aiohttp
import os
from datetime import datetime, timedelta, time
from zoneinfo import ZoneInfo

from aiohttp.web import RouteTableDef, run_app, Application, Request, Response, FileResponse, json_response

routes = RouteTableDef()

weather_cache = {}
forecast_data_cache = None
forecast_url = None

def normalize_course_input(course_str):
    course_str = course_str.strip().upper().replace(" ", "")
    index = 0
    while index < len(course_str) and not course_str[index].isdigit():
        index += 1
    if index == 0 or index == len(course_str):
        raise ValueError("Invalid course format")
    subject = course_str[:index]
    course_num = course_str[index:]
    if not course_num.isdigit() or len(course_num) != 3:
        raise ValueError("Invalid course number format")
    return subject, course_num

@routes.get('/')
async def index(request: Request) -> FileResponse:
    return FileResponse("index.html")

@routes.post('/weather')
async def POST_weather(request: Request) -> Response:
    global forecast_data_cache, forecast_url
    try:
        data = await request.json()
        course = data["course"]
        subject, course_num = normalize_course_input(course)
        course_name = f"{subject} {course_num}"
    except (ValueError, KeyError) as e:
        error_data = {
            "course": data.get("course", ""),
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": str(e)
        }
        return json_response(error_data, status=400)

    if course_name in weather_cache:
        response_data = {
            "course": course_name,
            **weather_cache[course_name]
        }
        return json_response(response_data, status=200)

    courses_microservice_url = os.getenv('COURSES_MICROSERVICE_URL')
    if not courses_microservice_url:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": "Courses microservice URL not set"
        }
        return json_response(error_data, status=500)

    url = f"{courses_microservice_url}/{subject}/{course_num}/"
    try:
        timeout = aiohttp.ClientTimeout(total=5)
        async with aiohttp.ClientSession(timeout=timeout) as session:
            async with session.get(url) as resp:
                if resp.status == 200:
                    course_data = await resp.json()
                    days_of_week = course_data.get("Days of Week", "")
                    start_time_str = course_data.get("Start Time", "")
                else:
                    error_response = {
                        "course": course_name,
                        "nextCourseMeeting": "",
                        "forecastTime": "",
                        "temperature": "",
                        "shortForecast": "",
                        "error": "Course not found"
                    }
                    return json_response(error_response, status=400)
    except asyncio.TimeoutError:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": "Timeout connecting to courses microservice"
        }
        return json_response(error_data, status=500)
    except Exception as e:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": f"Error connecting to courses microservice: {e}"
        }
        return json_response(error_data, status=500)

    if not days_of_week or not start_time_str:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": "Course schedule data is incomplete"
        }
        return json_response(error_data, status=400)

    day_map = {'M': 0, 'T': 1, 'W': 2, 'R': 3, 'F': 4, 'S': 5, 'U': 6}
    try:
        meeting_days = [day_map[day] for day in days_of_week]
        if not meeting_days:
            raise ValueError("No valid meeting days found")
        start_time = datetime.strptime(start_time_str, "%I:%M %p").time()
    except KeyError as e:
        error_data = {
            "course": course_name,
            "error": f"Invalid day abbreviation in '{days_of_week}': {e}"
        }
        return json_response(error_data, status=400)
    except ValueError as e:
        error_data = {
            "course": course_name,
            "error": f"Invalid start time format in '{start_time_str}': {e}"
        }
        return json_response(error_data, status=400)
    except Exception as e:
        error_data = {
            "course": course_name,
            "error": f"Invalid course schedule data: {e}"
        }
        return json_response(error_data, status=400)

    next_meeting = None
    now = datetime.now(ZoneInfo('America/Chicago'))

    today_weekday = now.weekday()
    if today_weekday in meeting_days:
        meeting_datetime = datetime.combine(now.date(), start_time, tzinfo=ZoneInfo('America/Chicago'))
        if meeting_datetime > now:
            next_meeting = meeting_datetime

    if next_meeting is None:
        for days_ahead in range(1, 30):
            meeting_date = now.date() + timedelta(days=days_ahead)
            meeting_weekday = meeting_date.weekday()
            if meeting_weekday in meeting_days:
                next_meeting = datetime.combine(meeting_date, start_time, tzinfo=ZoneInfo('America/Chicago'))
                break

    if next_meeting is None:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": "",
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": "Cannot determine next meeting time"
        }
        return json_response(error_data, status=400)

    try:
        if forecast_data_cache is None or forecast_url is None:
            timeout = aiohttp.ClientTimeout(total=5)
            async with aiohttp.ClientSession(timeout=timeout) as session:
                if forecast_url is None:
                    async with session.get('https://api.weather.gov/points/40.11,-88.24') as resp:
                        if resp.status == 200:
                            data = await resp.json()
                            forecast_url = data['properties']['forecastHourly']
                        else:
                            error_data = {
                                "course": course_name,
                                "nextCourseMeeting": next_meeting.strftime("%Y-%m-%d %H:%M:%S"),
                                "forecastTime": "",
                                "temperature": "",
                                "shortForecast": "",
                                "error": "Error fetching grid points from NWS API"
                            }
                            return json_response(error_data, status=500)
                async with session.get(forecast_url) as resp:
                    if resp.status == 200:
                        forecast_data_json = await resp.json()
                        forecast_data_cache = forecast_data_json['properties']['periods']
                    else:
                        error_data = {
                            "course": course_name,
                            "nextCourseMeeting": next_meeting.strftime("%Y-%m-%d %H:%M:%S"),
                            "forecastTime": "",
                            "temperature": "",
                            "shortForecast": "",
                            "error": "Error fetching forecast from NWS API"
                        }
                        return json_response(error_data, status=500)
        periods = forecast_data_cache

        next_meeting_utc = next_meeting.astimezone(ZoneInfo('UTC'))

        forecast_period = None
        for period in periods:
            period_start = datetime.fromisoformat(period['startTime'].replace('Z', '+00:00'))
            period_end = datetime.fromisoformat(period['endTime'].replace('Z', '+00:00'))
            if period_start <= next_meeting_utc < period_end:
                forecast_period = period
                break

        if forecast_period and 'temperature' in forecast_period and 'shortForecast' in forecast_period:
            forecast_data = {
                "temperature": forecast_period['temperature'],
                "shortForecast": forecast_period['shortForecast'],
                "forecastTime": next_meeting.strftime("%Y-%m-%d %H:%M:%S")
            }
        else:
            forecast_data = {
                "temperature": "forecast unavailable",
                "shortForecast": "forecast unavailable",
                "forecastTime": next_meeting.strftime("%Y-%m-%d %H:%M:%S")
            }
    except asyncio.TimeoutError:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": next_meeting.strftime("%Y-%m-%d %H:%M:%S"),
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": "Timeout connecting to NWS API"
        }
        return json_response(error_data, status=500)
    except Exception as e:
        error_data = {
            "course": course_name,
            "nextCourseMeeting": next_meeting.strftime("%Y-%m-%d %H:%M:%S"),
            "forecastTime": "",
            "temperature": "",
            "shortForecast": "",
            "error": f"Error connecting to NWS API: {e}"
        }
        return json_response(error_data, status=500)

    cache_entry = {
        "nextCourseMeeting": next_meeting.strftime("%Y-%m-%d %H:%M:%S"),
        "forecastTime": forecast_data.get("forecastTime", ""),
        "temperature": forecast_data.get("temperature", ""),
        "shortForecast": forecast_data.get("shortForecast", "")
    }
    weather_cache[course_name] = cache_entry

    response_data = {
        "course": course_name,
        **cache_entry
    }
    return json_response(response_data, status=200)

@routes.get('/weatherCache')
async def get_cached_weather(request: Request) -> Response:
    return json_response(weather_cache, status=200)

if __name__ == '__main__':
    import argparse
    parser = argparse.ArgumentParser()
    parser.add_argument('--host', type=str, default="0.0.0.0")
    parser.add_argument('-p', '--port', type=int, default=5000)
    args = parser.parse_args()

    app = Application()
    app.add_routes(routes)
    run_app(app, host=args.host, port=args.port)
```

### `schedules.py`

```python
"""
This file pulls from the Course Information System's public API,
as documented at https://courses.illinois.edu/cisdocs/explorer,
with local caching to prevent re-sending the same query many times.
The caching is durable on-disk and is perhaps over-eager:
once a course is queried it's first response is used forever,
even if the schedule is later updated.
To clear the cache for a particular term, remove the .jsonl file
for that term (e.g. `2024fall.jsonl`)
"""

import asyncio
import aiohttp

cache = {}

client = None

def loadcache(year,term):
    """Read an on-disk cache into memory"""
    import os, json
    made = cache.get((year,term), {})
    cache[(year,term)] = made
    if not os.path.exists(f'{year}{term}.jsonl'):
        return
    for line in open(f'{year}{term}.jsonl'):
        data = json.loads(line)
        made[tuple(data['key'])] = data['val']

def addtocache(year, term, subj, num, val):
    """Adds an entry to both on-disk cache and in-memory cache"""
    import json
    cache.setdefault((year, term), {})[(subj,num)] = val
    with open(f'{year}{term}.jsonl', 'a') as out:
        out.write(json.dumps({'key':[subj,num],'val':val}, separators=(',',':'))+'\n')

async def readcis(year, term, subj, num):
    """Queries and parses the XML-encoded CIS microservice"""
    import xml.etree.ElementTree as ET
    global client
    if client is None: client = aiohttp.ClientSession()

    async with client.get(f'http://courses.illinois.edu/cisapp/explorer/schedule/{year}/{term}/{subj}/{num}.xml?mode=cascade') as resp:
        if not resp.ok: return {'error':f'{subj} {num} not found in schedule'}
        root = ET.fromstring(await resp.text())
        ans = {}
        for sect in root.findall('.//detailedSection'):
            num = sect.findall('.//sectionNumber')[0].text
            for meeting in sect.findall('.//meeting'):
                kind = meeting.findall('.//type')[0].attrib['code']
                start, end, days = 'ARRANGED', 'ARRANGED', 'ARRANGED'
                try:
                    start = meeting.findall('.//start')[0].text
                    end = meeting.findall('.//end')[0].text
                    days = meeting.findall('.//daysOfTheWeek')[0].text
                except: pass
                ans[num+'-'+kind] = {'start':start, 'end':end, 'days':days}
        return ans
        

async def get_times(subj, num, term=None, year=None):
    """Given a subject and number, returns its full set of sections and meeting times"""
    import datetime
    today = datetime.date.today()
    if not year: year = today.year
    if not term: term = 'spring' if today.month < 6 else 'summer' if today.month < 8 else 'fall'

    if not isinstance(year, int): return {'error':'Year must be an integer'}
    if not isinstance(num, int): return {'error':'Course number must be an integer'}
    if not isinstance(subj, str): return {'error':'Course subject must be a string'}
    if year > today.year+1: return {'error':'Cannot look that far into the future'}
    if year < 2004: return {'error':'The catalog only goes back to 2004'}
    if term not in ('winter','spring','summer','fall'): return {'error':'Invalid term identifier'}
    if not subj or not all(ord('A') <= ord(char) <= ord('Z') for char in subj): return {'error':'Invalid course subject'}

    if (year, term) not in cache:
        await asyncio.to_thread(loadcache, year, term)
    if (subj, num) in cache[(year,term)]:
        return cache[(year,term)][(subj, num)]
    ans = await readcis(year, term, subj, num)
    await asyncio.to_thread(addtocache, year, term, subj, num, ans)
    return ans

def test_999():
    """This course is always 6 days 22 hours from now"""
    import datetime
    when = datetime.datetime.now() + datetime.timedelta(days=6,hours=22)
    return {
        'course': 'TEST 999',
        'Days of Week': 'MTWRFSU'[when.weekday()],
        'Start Time': when.strftime('%I:00 %p')
    }

import aiohttp.web
routes = aiohttp.web.RouteTableDef()

@routes.get('/{subject}/{number}/')
async def microservice(req):
    """The only web service endpoint in this microservice.
    Returns one of the meeting times for a course, preferring
    lectures to labs with various other tie-breaking rules.
    """
    subj = req.match_info['subject']
    num = req.match_info['number']
    try: num = int(num)
    except: pass
    if (subj,num) == ('TEST',999):
        return aiohttp.web.json_response(test_999())
    result = await get_times(subj, num)
    if 'error' in result:
        result['course'] = f'{subj} {num}'
        return aiohttp.web.json_response(result, status=404)
    best = ('',1000)
    order = ['LEC']+[f'L{n:<2}' for n in range(6)] + ['LCD'] + [f'S{n:<2}' for n in range(30)] + ['LBD', 'LBA', 'DIS', 'OD ', 'OLC', 'ONL', 'OLD']
    for k in result:
        if result[k]['start'] == 'ARRANGED': continue
        try: idx = order.index(k[4:])
        except ValueError: idx = -1
        if idx >= 0 and idx < best[-1]:
            best = (k, idx)
        elif best[-1] == 1000:
            best = (k, 999)
    if best[-1] == 1000:
        return aiohttp.web.json_response({'course':f'{subj} {num}', 'error':'No scheduled meetings found'}, status=404)
    return aiohttp.web.json_response({
        'course':f'{subj} {num}',
        'Days of Week': result[best[0]]['days'].strip(),
        'Start Time': result[best[0]]['start'],
    })



if __name__ == '__main__': 
    import os
    os.chdir(os.path.dirname(__file__))

    app = aiohttp.web.Application()
    app.add_routes(routes)
    aiohttp.web.run_app(app, host='0.0.0.0', port=34000) # this function never returns
    if client is not None:
        asyncio.run(client.close())
```

### `index.html`

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>CS 340: Weather Microservice</title>
    <script>
    function sendPOST() {
      let formData = { course: document.getElementById('course').value };
      fetch('/weather', {
        method: 'POST',
        body: JSON.stringify(formData),
        headers: {'Content-Type': 'application/json'},
      }).then(res => res.json()).then(data => {
        if ('error' in data) {
          document.getElementById('content').innerHTML = `<hr><h3>Error</h3><p>${JSON.stringify(data)}</p>`;
        } else {
          let meetingDate = new Date(Date.parse(data.nextCourseMeeting));
          let meetingDateStr = meetingDate.toLocaleString('en-US', {
            weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric', minute: '2-digit', second: '2-digit'
          })

          let forecastDate = new Date(Date.parse(data.forecastTime));
          let forecastDateStr = forecastDate.toLocaleString('en-US', {
            weekday: 'long', year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric', minute: '2-digit', second: '2-digit'
          })        

          document.getElementById('content').innerHTML = `<hr><h3>Weather for the next ${data.course} meeting</h3><ul><li>Next Meeting: ${meetingDateStr}</li><li>Forecast Time: ${forecastDateStr}</li><li>Temperature: ${data.temperature}°F</li><li>Forecast: ${data.shortForecast}</li></ul>`
        }
      }).catch(err => {
        document.getElementById('content').innerHTML = `<hr><h3>Error ${err.name}</h3><p>${err.message}</p>`;
      })
    };
    </script>    
  </head>
  <body>
      <h1>Weather of Your Next Course Meeting</h1>
      <p>
        <label>Course: <input type="text" id="course" placeholder="Course Subject and Number"></label>
        <button onclick="sendPOST();">Get Weather!</button>
      </p>
      <div id="content"></div>
    </div>
  </body>
</html>
```
