# This is a collection of different scripts collected over time.
- [This is a collection of different scripts collected over time.](#this-is-a-collection-of-different-scripts-collected-over-time)
  - [Python](#python)
    - [Download Video in segments and save to file](#download-video-in-segments-and-save-to-file)


## Python

### Download Video in segments and save to file from ARD Mediathek
```python
import requests
import tqdm  # not needed just makes the console pretty

threshold = 5
segment_start = 0
segment_end=100
count = 0
save_file_name = 'somefilename.mp4'

with open(save_file_name, 'wb+') as file:
    for segment_ID in tqdm.tqdm(range(segment_start,segment_end,1)):
        url = f'SOMEURLYOUWANTTODOWNLAOD'  #....,.s,.sm,.m,.ml,.l,.xl,.xxl,.mp4.csmil/segment{segment_ID}_5_av.ts?null=0'
        r = requests.get(url)
        if r.status_code is 200:
            file.write(r.content)
        else:
            count = count +1
        if count is threshold:
            print("To many segments missing. Stopping Download.")
    file.close() #
```

### Show all Outlook Events based on user input
Usage : "Name of Event" {Number of days to look into the future}(default is 365)

```python
# https://docs.microsoft.com/en-us/dotnet/api/microsoft.office.interop.outlook.mailitem?redirectedfrom=MSDN&view=outlook-pia#properties_

import win32com.client  # -> muss installiert werden "pip install pywin32"
import datetime
import sys
from prettytable import PrettyTable
outlook = win32com.client.Dispatch("Outlook.Application").GetNamespace("MAPI")
# accounts = win32com.client.Dispatch("Outlook.Application").Session.Accounts
# # -> wählt den Mail Account
# inbox = outlook.Folders(accounts[0].DeliveryStore.DisplayName)


def getMeetingDetails(a):
    start = datetime.datetime.strptime(
        str(a.Start)[:-6], "%Y-%m-%d %H:%M:%S").strftime("%d.%m.%y %H:%M:%S")
    val = [start, a.Subject[:50], a.Organizer, a.Location[:50]]
    return val


def getCalenderEvents(meeting, day):
    today = datetime.datetime.today()
    begin = today.date().strftime("%d.%m.%Y")
    endOfLookup = datetime.timedelta(days=day)+today
    end = endOfLookup.date().strftime("%d.%m.%Y")

    restrictions = "[Start] >= '" + begin + "' AND [END] <= '" + end + "'"

    appointments = outlook.GetDefaultFolder(9).Items
    appointments.Sort("[Start]")
    appointments.IncludeRecurrences = "True"
    appointments = appointments.Restrict(restrictions)

    table = PrettyTable()
    table.field_names = ["Start", "Subject", "Organizer", "Location"]
    table.align = "l"

    if meeting is "*":
        for appointment in appointments:
            table.add_row(getMeetingDetails(appointment))
    else:
        for appointment in appointments:
            if (meeting.lower() in appointment.Subject.lower()) or (meeting.lower() in appointment.Organizer.lower()):
                table.add_row(getMeetingDetails(appointment))
    return table


if __name__ == "__main__":
    meeting = sys.argv[1]
    days = 365
    if len(sys.argv)-1 >= 2:
        days = sys.argv[2]
    msg = f"[*] Checking for next meetings containing '{meeting}' within the next {days} days"
    print(msg)
    print(getCalenderEvents(meeting, int(days)))



```

### Bulk Download items like pdfs and save them to file
```python
import requests
import tqdm as tqdm
list = []


def download_and_save(item, save_file_name):
    with open(save_file_name, 'wb+') as file:
        r = requests.get(item)
        if r.status_code is 200:
            file.write(r.content)
        else:
            print(f'{item} -> Error downloading')
    file.close()


for item in tqdm.tqdm(list):
    save_file_name = "".join(item.split("_", 1)[1])
    download_and_save(item, save_file_name)
```
