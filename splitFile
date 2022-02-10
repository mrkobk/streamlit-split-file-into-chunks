#!/usr/bin/env python
# coding: utf-8

# In[2]:

import streamlit as st
from pandas import read_excel, read_csv
import csv
import xlrd
from zipfile import ZipFile
from io import BytesIO
import base64

# In[4]:

st.set_page_config(layout="wide")
st.title("Split your Excel/CSV file into chunks")
st.sidebar.image("https://cdn-icons-png.flaticon.com/128/107/107139.png")
st.sidebar.header("What's this tool about?")
st.sidebar.write('''
		This little helper allows you to break up a larger Excel or CSV file into n smaller files.
		This can be useful if you need to upload xlsx/csv files somewhere but the tool has a entry limit per file. 
		E.x.: you want to update 3000000 keywords, but the tool provider only allows to upload CSV files with 30000 keywords each.
		It can also be used to extract a sample of the data in case you cannot open your file because of Excel freezing.
		
		Upload a (large) XLSX or CSV file, define the amount of rows you want each file to contain
		as well as whether you want the output files to be CSV or Excel files
			''')

rows = st.sidebar.number_input("Enter number of rows per file: (e.x. 10000) and hit enter ", min_value=1, value=10000)		
menu = ["CSV","XLSX"]
choice = st.sidebar.selectbox("Select the output format of your files",menu)

if choice == "CSV":
	st.subheader("You selected the output to be CSV files")

else:
	st.subheader("You selected the output to be Excel files")
	
upload = st.file_uploader("Upload either a Excel or CSV file you want to split into smaller files", type=["xlsx","csv"])

if upload is not None:

	fileName = upload.name
	fileName = fileName.split(".")[0]
		
	if upload.type == "text/csv":
		df = read_csv(upload)
		
	else:
		df = read_excel(upload, sheet_name=0)
	
	total = len(df)
	
	counter = 0
	zipObj = ZipFile("chunkFiles.zip", "w")

	for i in range(0,total,rows):
  		j = min(total, i + rows)
  		
  		if choice == "XLSX":
  			df.iloc[i:j].to_excel(f"{fileName}_rows_{i}_{j}.xlsx", encoding="utf-16", index=False)
  			zipObj.write(f"{fileName}_rows_{i}_{j}.xlsx")
  		
  		else:
  			df.iloc[i:j].to_csv(f"{fileName}_rows_{i}_{j}.csv", encoding="utf-16", index=False)
  			zipObj.write(f"{fileName}_rows_{i}_{j}.csv")
  		
  		counter += 1
  		
	zipObj.close()
	
	ZipfileDotZip = "chunkFiles.zip"
	st.success(f"Success! {counter} {choice} files with each {int(rows)} rows have been created and are ready for download.")
	
	with open(ZipfileDotZip, "rb") as f:
		bytes = f.read()
		b64 = base64.b64encode(bytes).decode()
		href = f"<a href=\"data:file/zip;base64,{b64}\" download='{ZipfileDotZip}'>\
		Download files</a>"
		
		st.markdown(href, unsafe_allow_html=True)
