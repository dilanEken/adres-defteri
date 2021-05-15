# adres-defteri
from msilib import add_tables
from tkinter import filedialog # dosya çagırma
from msilib import Table
from tkinter import * # tkinter kütüphanesi
from tkinter import messagebox # kullanıcıyı bilgilendirme
import sqlite3 # sqlite kütüphanesi
pen=Tk()
pen.geometry("800x600")
pen.title("ADRESLER")
baglanti = sqlite3.connect("adres_defteri.db")
veri = baglanti.cursor()
veri.execute("CREATE TABLE IF NOT EXISTS adres_defteri(id INTEGER ,adi_soyadi text, telefon int,note text)")
adres_defteri = []

def gözaltın():
    filepath = filedialog.askopenfilename()
    print(filepath)
def temizle():
    ad_e.delete(0, "end")
    telefon_e.delete(0, "end")
    fotograf_e.delete(0, "end")
    notu_e.delete(0, "end")
def kaydet():
    adi_soyadı = ad_e.get()
    tel = telefon_e.get()
    note = notu_e.get()
    if len(adi_soyadı) == 0:
        messagebox.showinfo("Bilgiler Boş", "Lütfen alanları doldurup eklemeyi deneyin")
    else:
        adres_defteri.append(adi_soyadı)
        veri.execute("INSERT INTO adres_defteri(adi_soyadi,telefon,note) values(?,?,?)", (adi_soyadı, tel, note))
        ad_e.delete(0, "end")
        telefon_e.delete(0, "end")
        notu_e.delete(0,"end")
        messagebox.showinfo("İşlem Başarılı", "Bilgiler Başarıyla Kaydedildi.")
        baglanti.commit()


Tbl = []

def tablo_doldur(a):
    if a == 1:
        veri.execute("SELECT * FROM adres_defteri")

    elif a==2:
        veri.execute("SELECT * FROM adres_defteri order by adi_soyadi")
    rows = veri.fetchall()
    Tbl = rows

    return Tbl


vtablo = tablo_doldur(1)



def tablo2():
    vtablo = tablo_doldur(2)

    Table(fram_alt_sag, vtablo)

def isme_göre():
    temizle()
    adi_soyadi = ad_ara_e.get()
    veri.execute("SELECT * FROM adres_defteri where adi_soyadi like '%" + adi_soyadi + "%' limit 1")
    rows = veri.fetchall()
    tut = rows[:1]
    listut = tut[0]
    idtut = listut[0:1]
    listedenal1 = listut[1:2]
    listedenal2 = listut[2:3]
    listedenal3 = listut[3:4]
    ad_e.insert('end', listedenal1)
    telefon_e.insert('end', listedenal2)
    notu_e.insert('end', listedenal3)
def telefona_göre():
    temizle()
    telefon_no = telefon_ara_e.get()
    veri.execute("SELECT * FROM adres_defteri where telefon like '%" + telefon_no + "%' limit 1")
    rows = veri.fetchall()
    tut = rows[:1]
    listut = tut[0]
    listedenal1 = listut[1:2]
    listedenal2 = listut[2:3]
    listedenal3 = listut[3:4]
    print(listedenal1)
    print(listedenal2)
    print(listedenal3)
    ad_e.insert('end', listedenal1)
    telefon_e.insert('end', listedenal2)
    notu_e.insert('end', listedenal3)

    utut = len(tut[0])
    atut = tut[0:1]
    print(utut)
    print(atut)


def kapat():
    pen.destroy() # pencereyi kapat
def Enterbasad(event):
    isme_göre()

def Enterbastelefon(event):
    telefona_göre()

adres=Label(pen, text="ADRES BİLGİSİ",font=('Arial', 23,'bold')).place(x=20, y=40)
ad_ara_label=Label(pen,text="İsme göre ara").place(x=300, y=20)
ad_telefonu_label=Label(pen,text="Telefona göre ara").place(x=300, y=50)

ad=Label(pen,text="AD").place(x=30, y=100)
tel=Label(pen,text="TELEFON").place(x=30, y=140)
foto=Label(pen,text="FOTOĞRAF").place(x=30, y=180)
notumuz=Label(pen,text="NOTU").place(x=30, y=220)
resim=PhotoImage(file="images/user1.png")
testButton = Button(pen, image=resim).place(x=540,y=0,height=280,width=210)


ad_ara_e=Entry(pen).place(x=400,y=20)
telefon_ara_e=Entry(pen).place(x=400,y=50)

ad_e=Entry(pen).place(x=100,y=100,width=350,height=30)
telefon_e=Entry(pen).place(x=100,y=140,width=350,height=30)
fotograf_e=Entry(pen).place(x=100,y=180,height=30,width=180)
gözalt=Button(pen, text="GözAlt",fg="brown",bg="gray",command=gözaltın).place(x=300,y=180,width=150,height=30)
notu_e=Entry(pen).place(x=100,y=220,width=350,height=30)
#butonlar
kaydet=Button(pen,text="kaydet",bg="salmon",command=kaydet).place(x=300, y=260,width=150,height=40)
k_ekle=Button(pen,text="kayıt ekle",bg="gold2",command=temizle).place(x=10, y=320,width=100,height=30)
k_sil=Button(pen,text="kayıt sil",bg="gold2").place(x=10, y=360,width=100,height=30)
k_düzenle=Button(pen,text="kaydı düzenle",bg="gold2").place(x=10, y=400,width=100,height=30)
k_sırala=Button(pen,text="isme göre",bg="gold2",command=tablo2).place(x=10, y=440,width=100,height=30)
k_çık=Button(pen,text="çıkış",command=kapat,bg="gold2").place(x=10,y=480,width=100,height=30)
#frame dosyalrı
fram_alt_sag= Frame (pen,relief='solid',borderwidth=2,bg="plum2").place(x=130, y=310, width=620, height=280)
fram_alt_baslik = Frame(fram_alt_sag, relief='solid',borderwidth=3).place(x=130, y=310, width=620, height=40)
#sqlite veri veri
tablo_baslık_ıd =Label(fram_alt_baslik, text="ID", font=('Arial', 13),width=4,borderwidth=1, relief='solid').place(x=140, y=315,height=30,width=80)
tablo_baslık_ad_soy=Label(fram_alt_baslik, text="Adı Soyadı", font=('Arial', 13),width=18,borderwidth=1, relief='solid').place(x=220, y=315,height=30,width=150)
tablo_baslik_telefon =Label(fram_alt_baslik, text="Telefon", font=('Arial', 13),width=13,borderwidth=1, relief='solid').place(x=370, y=315,height=30,width=150)
tablo_baslik_notu =Label(fram_alt_baslik, text="Not", font=('Arial', 13),width=14,borderwidth=1, relief='solid').place(x=500, y=315,height=30,width=220)


ad_ara_e.bind('<Return>', Enterbasad)
telefon_ara_e.bind('<Return>', Enterbastelefon)
total_rows = len(vtablo)
total_columns = len(vtablo[0])
class Table:
    def __init__(self, pen, vtablo):
        for i in range(total_rows):
            for j in range(total_columns):
                if j == 0:
                    self.s = Entry(pen)
                elif j == 1:
                    self.s = Entry(pen)
                elif j == 2:
                    self.s = Entry(pen)
                elif j == 3:
                    self.s = Entry(pen)
                else :
                    self.s = Entry(pen,)

                self.s.place(x=i,y=j)
                self.s.insert(END, vtablo[i][j])





Table(fram_alt_sag, vtablo)

pen.mainloop()
baglanti.commit()
baglanti.close()



