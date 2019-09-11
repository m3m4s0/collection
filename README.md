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