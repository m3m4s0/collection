# This is a collection of different scripts collected over time.
- [This is a collection of different scripts collected over time.](#this-is-a-collection-of-different-scripts-collected-over-time)
  - [Python](#python)
    - [Download Video in segments and save to file](#download-video-in-segments-and-save-to-file)


## Python

### Download Video in segments and save to file
```python
import requests
import tqdm  # not needed just makes the console pretty


threshold = 5
count = 0
save_file_name = 'somefilename.extension'

with open(save_file_name, 'wb+') as file:
    for segment_ID in tqdm.tqdm(range(1,40,1)):
        url = f'SOMEURLYOUWANTTODOWNLAOD'
        r = requests.get(url)
        if r.status_code is 200:
            print(f'{segment_ID} -> Found')
            file.write(r.content)
        else:
            count = count +1
        if count is threshold:
            print("To many segments missing. Stopping Download.")
    file.close() #
```

### Show all Outlook Events based on user input

```python
# https://docs.microsoft.com/en-us/dotnet/api/microsoft.office.interop.outlook.mailitem?redirectedfrom=MSDN&view=outlook-pia#properties_

import win32com.client  # -> muss installiert werden "pip install pywin32"
import csv
import datetime
import sys
outlook = win32com.client.Dispatch("Outlook.Application").GetNamespace("MAPI")
accounts = win32com.client.Dispatch("Outlook.Application").Session.Accounts
# -> wählt den Mail Account
inbox = outlook.Folders(accounts[0].DeliveryStore.DisplayName)


def getCalenderEvents(meeting, day):
    appointments = outlook.GetDefaultFolder(9).Items
    appointments.Sort("[Start]")
    appointments.IncludeRecurrences = "True"
    today = datetime.datetime.today()
    begin = today.date().strftime("%d.%m.%Y")
    endOfLookup = datetime.timedelta(days=day)+today
    end = endOfLookup.date().strftime("%d.%m.%Y")
    restrictions = "[Start] >= '" + begin + "' AND [END] <= '" + end + "'"
    appointments = appointments.Restrict(restrictions)
    events = []
    for a in appointments:
        if meeting in a.Subject:
            event = f"{a.Subject} - {a.Start}"
            events.append(event)
    return events


if __name__ == "__main__":
    meeting = sys.argv[1]
    days = 365
    if len(sys.argv)-1 >= 2:
        days = sys.argv[2]
    print(f"[*] Checking {meeting}")
    for event in getCalenderEvents(meeting, int(days)):
        print(event)



```

## Node Js
