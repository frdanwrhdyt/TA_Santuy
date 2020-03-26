#=komen dlm bahasa python
#---------------------Deklarasi library---------------------#
import minimalmodbus
from time import sleep
import datetime
import json
import requests
import mysql.connector
#---------------------Deklarasi library---------------------#

#---------------------Deklarasi Database--------------------#
db   = mysql.connector.connect( #Deklarasi MySQL
	host     = "localhost",
	user     = "root",
	passwd   = "7801231",
	database = "hasil_Baca"
)
cur = db.cursor() #Kursor buat databasenya
#---------------------Deklarasi Database--------------------#
instrument = minimalmodbus.Instrument('/dev/ttyUSB0',1) #Deklarasi PORT
instrument.serial.baudrate = 9600 #Deklarasi bandwith

def read_meter():
#-----------------Mengambil data dari modbus----------------#
	CURR = float(instrument.read_float(0x0BB7)) #total arus
	VOLT = float(instrument.read_float(0x0BD3)) #tolal tegangan 
	POWF = float(instrument.read_float(0x0C77)) #daya factor
	ACTP = float(instrument.read_float(0x0BED)) #daya aktif
	RELP = float(instrument.read_float(0x0BFB)) #daya relatif
	APPP = float(instrument.read_float(0x0C03)) #daya semu
	TIME = str(datetime.datetime.now()) #Hitung waktu & tanggal sekarang
#-----------------Mengambil data dari modbus----------------#

#------------Menyatukan data modbus jadi 1 array------------#
	DATA = [TIME,CURR,VOLT,POWF,ACTP,RELP,APPP] #Menyatukan data menjadi array
	VAR  = ['tanggal','arus', 'tegangan','factor daya','daya aktif','daya relatif','daya semu'] #Deklarasi array di atas
#------------Menyatukan data modbus jadi 1 array------------#

	i = len(VAR) #Menghitung jumlah anggota array di atas
  
#--------------------Menulis ke terminal-------------------#
  for j in range (0,i):
		print(str(VAR[j]) + ' = ' + str(DATA[j]))
#--------------------Menulis ke terminal-------------------#

#----------------------Memanggil fungsi--------------------#
	send_Ant(TIME,CURR,VOLT,POWF,ACTP,RELP,APPP)   
	send_MySQL(TIME,CURR,VOLT,POWF,ACTP,RELP,APPP)
#----------------------Memanggil fungsi--------------------#

	sleep(300) #Satuan detik

#-------------------Mengirim data ke Antares---------------#
def send_Ant(TIME,CURR,VOLT,POWF,ACTP,RELP,APPP): 
	data = '\r\n{\r\n  "m2m:cin": {\r\n    "cnf": "message",\r\n    "con": "\r\n      {\r\n      \t \\"Tanggal\\": \\"'+str(TIME)+'\\",\r\n         \\"Arus\\": \\"'+str(CURR)+'\\",\r\n         \\"Tegangan\\": \\"'+str(VOLT)+'\\",\r\n         \\"Faktor Daya\\": \\"'+str(POWF)+'\\"\r\n         \\"Daya Aktif\\": \\"'+str(ACTP)+'\\",\r\n         \\"Daya Reaktif\\": \\"'+str(RELP)+'\\"\r\n         \\"Daya Semu\\": \\"'+str(APPP)+'\\",\r\n}"}}'
	url = 'https://platform.antares.id:8443/~/antares-cse/antares-id/Modbus_reader/Trial_GdN'
	headers = {'cache-control':'no-cache','content-type':'application/json;ty=4','x-m2m-origin':'2ce124cbd083f559:10ec3f490fb485c3'}
	r = requests.post(url,headers=headers,data=data)
#-------------------Mengirim data ke Antares---------------#

#----------Mengirim data ke MySQL (local database)---------#
def send_MySQL(TIME,CURR,VOLT,POWF,ACTP,RELP,APPP): 
	sql="""INSERT INTO hasilBaca (datetime,arus,tegangan,faktor_daya,daya_aktif,daya_relatif,daya_semu) VALUES(%s,%s,%s,%s,%s,%s,%s)"""
	val=(str(TIME),str(CURR),str(VOLT),str(POWF),str(ACTP),str(RELP),str(APPP))
	try:
		print ("Writing to the database...")
		cur.execute(sql,val)
		db.commit()
		print ("Write complete")
	except:
		db.rollback()
		print ("Try again")
#----------Mengirim data ke MySQL (local database)---------#

#---------------------Fungsi perulangan--------------------#
while True:      
    sleep(1)     
    read_meter() #Memanggil fungsi
#---------------------Fungsi perulangan--------------------#
