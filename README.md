in City_FIPS_csv.zip:

1. manually prepare LAdata.csv as LA cities with population data, (and sub-community data) 
2. manually prepare CAdata.csv as CA cities with FIPS code, per Census department in 2016, 
in form of Summary Level,State Code,County Code,Place Code
Note that some "large city" have non-zero county code and zeor place code
some "small city" have zero county code but non-zero place code
3. run below python to generate LAdata_output.csv with LA cities, FIPS and population



import re
import csv

LApath='/Users/mahannan/Audia/Pictia/LAdata.csv'
CApath='/Users/mahannan/Audia/Pictia/CAdata.csv'

#finalcsv=open(csvpath, "r")
#finalcsv.write("city, Mar27 confirmed, Mar27 heat, Mar26 confirmed, Mar26 heat, Mar25 confirmed, Mar25 heat, Mar24 confirmed, Mar24 heat, Mar23 confirmed, Mar23 heat, Mar22 confirmed, Mar22 heat, Mar21 confirmed, Mar21 heat, Mar20 confirmed, Mar20 heat, Mar19 confirmed, Mar19 heat, Mar18 confirmed, Mar18 heat, Mar17 confirmed, Mar17 heat,\n")
#finalcsv.close()
#finalreader=csv.DictReader(finalcsv)
#for row in finalreader:
match=0
idx=0
with open(LApath) as LAfile:
	LAreader=csv.DictReader(LAfile)
	outputpath='/Users/mahannan/Audia/Pictia/output.csv'
	finaloutput=open(outputpath, "w")
	outputwriter=csv.DictWriter(finaloutput,fieldnames=LAreader.fieldnames)	
	for LArow in LAreader:
		idx=idx+1
		#if(idx<2):continue
		#LA row city,population
		#print LArow
		LAname=LArow['city']
		LAname=LAname.replace(' ', '')
		LAname=LAname.lower()
		#LApopu=LArow['population']
		with open(CApath) as CAfile:
			CAreader = csv.DictReader(CAfile)
			for CArow in CAreader:
				#CA row name,Summary Level,State Code,County Code,Place Code
				#print CArow
				CAname=CArow['name']
				CAname=CAname.replace(' ', '')
				CAname=CAname.lower()
				find=0
				if (CAname.find(LAname) != -1): find=1
				if (LAname.find(CAname) != -1): find=1
				if (find>0):
					#LApopu=LArow['population']
					LArow['Summary Level']=CArow['Summary Level']
					LArow['State Code']=CArow['State Code']
					LArow['County Code']=CArow['County Code']
					LArow['Place Code']=CArow['Place Code']
		outputwriter.writerow(LArow)


4. run below python, put above-mentioned FIPS into the Mar17-27 confirmed case csv as March_FIPS_output.csv


import re
import csv

LApath='/Users/mahannan/Audia/Pictia/LAdata.csv'
CApath='/Users/mahannan/Audia/Pictia/CAdata.csv'


csvpath='/Users/mahannan/Audia/Pictia/LIST.csv'
finalcsv=open(csvpath, "r")
#finalcsv.write("city, Mar27 confirmed, Mar27 heat, Mar26 confirmed, Mar26 heat, Mar25 confirmed, Mar25 heat, Mar24 confirmed, Mar24 heat, Mar23 confirmed, Mar23 heat, Mar22 confirmed, Mar22 heat, Mar21 confirmed, Mar21 heat, Mar20 confirmed, Mar20 heat, Mar19 confirmed, Mar19 heat, Mar18 confirmed, Mar18 heat, Mar17 confirmed, Mar17 heat,\n")
#finalcsv.close()
#finalreader=csv.DictReader(finalcsv)


#for row in finalreader:
	




match=0
idx=0
with open(csvpath) as csvfile:
	final=csv.DictReader(csvfile)
	outputpath='/Users/mahannan/Audia/Pictia/output.csv'
	finaloutput=open(outputpath, "w")
	outputwriter=csv.DictWriter(finaloutput,fieldnames=final.fieldnames)	
	for row in final:
		idx=idx+1
		if(idx<2):continue
		for date in range(17,27):
			outpath='/Users/mahannan/Audia/Pictia/Mar'+str(date)+'.csv'
			with open(outpath) as csvfile2:
				reader = csv.DictReader(csvfile2)
				for row2 in reader:
					str1=str(row['city'])
					str2=str(row2['city'])
					#print str1+'.', " with ", str2+'.'
					#str1.replace(" ","fark")
					#str2.replace(" ","fark")
					#print str1+'.', " after ", str2+'.'
					#if -1<str2.find(str1)<3:
					if str1 == str2:
						strz=str(date)
						strx=' Mar'+strz+' confirmed'
						stry=' Mar'+strz+' heat'
						row[strx]=row2[' confirmed cases']
						row[stry]=row2[' heat label']
		with open(CApath) as CAfile:
			CAreader = csv.DictReader(CAfile)
			for CArow in CAreader:
				#CA row name,Summary Level,State Code,County Code,Place Code
				#print CArow
				LAname=row['city']
				LAname=LAname.replace(' ', '')
				LAname=LAname.lower()
				CAname=CArow['name']
				CAname=CAname.replace(' ', '')
				CAname=CAname.lower()
				find=CAname.find(LAname) 
				if (find>=0):
					if (find <3):
						print 'CArow ',CArow
						print 'LArow ',row['city']
						#LApopu=LArow['population']
						row['Summary Level']=CArow['Summary Level']
						row['State Code']=CArow['State Code']
						row['County Code']=CArow['County Code']
						row['Place Code']=CArow['Place Code']
		outputwriter.writerow(row)
