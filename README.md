# whatsapp_analysis
## NSSF INTERN WHASTAPP GROUP ANALYSIS
### first we start with importing neccesary libraries
such as
* pandas 
* numpy
* matplotlib
```
import regex
import pandas as pd
import numpy as np
import emoji
from collections import Counter
import matplotlib.pyplot as plt
from wordcloud import WordCloud, STOPWORDS, ImageColorGenerator
```
#### Step 2 
#### Add python functions
```def date_time(s):
    pattern = '^([0-9]+)(\/)([0-9]+)(\/)([0-9]+), ([0-9]+):([0-9]+)[ ]?(AM|PM|am|pm)? -'
    result = regex.match(pattern, s)
    if result:
        return True
    return False
def find_author(s):
    s = s.split(":")
    if len(s)==2:
        return True
    else:
        return False
def getDatapoint(line):
    splitline = line.split(' - ')
    dateTime = splitline[0]
    date, time = dateTime.split(", ")
    message = " ".join(splitline[1:])
    if find_author(message):
        splitmessage = message.split(": ")
        author = splitmessage[0]
        message = " ".join(splitmessage[1:])
    else:
        author= None
    return date, time, author, message
   ```

  ###  importing data 
 ```
   
   data = []
conversation = 'WhatsApp Chat with NSSF INTERNS.txt'
with open(conversation, encoding="utf-8") as fp:
    fp.readline()
    messageBuffer = []
    date, time, author = None, None, None
    while True:
        line = fp.readline()
        if not line:
            break
        line = line.strip()
        if date_time(line):
            if len(messageBuffer) > 0:
                data.append([date, time, author, ' '.join(messageBuffer)])
            messageBuffer.clear()
            date, time, author, message = getDatapoint(line)
            messageBuffer.append(message)
        else:
        
            messageBuffer.append(line)
 ```
            
 ### Take a look of the data
 ```
     df = pd.DataFrame(data, columns=["Date", 'Time', 'Author', 'Message'])
df['Date'] = pd.to_datetime(df['Date'])
print(df.tail(20))
print(df.info())
print(df.Author.unique())

### Number of whatsapp messages
```
total_messages = df.shape[0]
print(total_messages)

### Number of media messages
```
media_messages = df[df["Message"]=='<Media omitted>'].shape[0]
print(media_messages)
```
### Find the number of emoji present in whatsapp chats and final insights
```
URLPATTERN = r'(https?://\S+)'
df['urlcount'] = df.Message.apply(lambda x: regex.findall(URLPATTERN, x)).str.len()
links = np.sum(df.urlcount)
print("Chats between +254 729 205284 and +254 729 940288")
print("Total Messages: ", total_messages)
print("Number of Media Shared: ", media_messages)
print("Number of Emojis Shared", emojis)
print("Number of Links Shared", links
