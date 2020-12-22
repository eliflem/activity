#!/usr/bin/env python
# coding: utf-8

# In[19]:


#import libraries
import streamlit as st
import requests as r
import pandas as pd
import datetime
import os
import streamlit as st
import base64


# In[2]:


filter_id = st.text_input("Please enter filter id:")
token = st.text_input("Please enter your token:")
from datetime import date
today = date.today()
d1 = today.strftime("%B %d")


# In[3]:


filters=r.get("https://api.pipedrive.com/v1/deals?filter_id="+filter_id+"&status=all_not_deleted&start=-1&limit=1000&api_token="+token)
filter_deals = filters.json()

created = {}
ids = []
for deal in filter_deals["data"]:
    deal_ids = deal["id"]
    ids.append(deal_ids)
    created[deal_ids] = deal["add_time"] #dealcreated

deals_ids = [str(i) for i in ids] #deal ids


# In[4]:


urls=[]

for deal in deals_ids:
    urls.append("https://api.pipedrive.com/v1/deals/"+deal+"/activities?api_token="+token)

deals=[]
for url in urls:
    response = r.get(url)
    deals.append(response.json())


# In[19]:


deals


# In[5]:


results = {}
for deal in deals:
    if deal["data"] is not None:
        for data in deal["data"]:
            id = data["deal_id"]
            if id in results:
                if data["add_time"] < results[id]:
                    results[id] = data["add_time"] 
            else:
                results[id] = data["add_time"]  


# In[6]:


st.write(results)


# In[20]:


df = pd.DataFrame(list(results.items()), columns = ["Deal ID", "Activity"])
csv = df.to_csv(index=False)
b64 = base64.b64encode(csv.encode()).decode()
st.markdown('### **⬇️ Download output CSV File **')
href = f'<a href="data:file/csv;base64,{b64}">Download CSV File</a> (right-click and save as ".csv")'
st.markdown(href, unsafe_allow_html=True)


# final = {}
# for x in created:
#     if x in results:
#         created_datetime = datetime.datetime.strptime(created[x], '%Y-%m-%d %H:%M:%S')
#         result_datetime = datetime.datetime.strptime(results[x], '%Y-%m-%d %H:%M:%S')
#         duration = result_datetime - created_datetime
#         duration_sec = duration.total_seconds()
#         minutes = divmod(duration_sec, 60)[0]
#         final[x] = minutes
#     else:
#         final[x] = "no activity"

# path = os.path.dirname(os.path.realpath("__file__"))
# df = pd.DataFrame(list(final.items()), columns = ["Deal ID", "Difference(min)"])
# df.to_csv(path+"\\"+d1+".csv", index=False)
