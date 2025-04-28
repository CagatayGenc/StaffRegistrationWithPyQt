```python
import sys
import csv
from PyQt5.QtWidgets import (
    QApplication, QWidget, QLabel, QLineEdit, QPushButton, QVBoxLayout, QHBoxLayout,
    QMessageBox, QTableWidget, QTableWidgetItem, QHeaderView
)
from PyQt5.QtCore import QDate

class PersonelKayitSistemi(QWidget):
    def __init__(self):
        super().__init__()
        self.initUI()

    def initUI(self):
        self.setWindowTitle('Personel Kayıt Sistemi')
        self.setGeometry(100, 100, 600, 400)

        # Widget'ları oluştur
        self.tc_label = QLabel('TC Kimlik No:')
        self.tc_input = QLineEdit(self)

        self.ad_label = QLabel('Ad:')
        self.ad_input = QLineEdit(self)

        self.soyad_label = QLabel('Soyad:')
        self.soyad_input = QLineEdit(self)

        self.dogum_tarihi_label = QLabel('Doğum Tarihi:')
        self.dogum_tarihi_input = QLineEdit(self)
        self.dogum_tarihi_input.setPlaceholderText('GG/AA/YYYY')

        self.kaydet_button = QPushButton('Kaydet', self)
        self.kaydet_button.clicked.connect(self.kaydet)

        # Tablo oluştur
        self.tablo = QTableWidget(self)
        self.tablo.setColumnCount(4)
        self.tablo.setHorizontalHeaderLabels(['TC Kimlik No', 'Ad', 'Soyad', 'Doğum Tarihi'])
        self.tablo.horizontalHeader().setSectionResizeMode(QHeaderView.Stretch)

        # Layout'u ayarla
        vbox = QVBoxLayout()

        # Form layout
        form_layout = QVBoxLayout()
        form_layout.addWidget(self.tc_label)
        form_layout.addWidget(self.tc_input)
        form_layout.addWidget(self.ad_label)
        form_layout.addWidget(self.ad_input)
        form_layout.addWidget(self.soyad_label)
        form_layout.addWidget(self.soyad_input)
        form_layout.addWidget(self.dogum_tarihi_label)
        form_layout.addWidget(self.dogum_tarihi_input)
        form_layout.addWidget(self.kaydet_button)

        # Ana layout
        vbox.addLayout(form_layout)
        vbox.addWidget(self.tablo)

        self.setLayout(vbox)

        # CSV dosyasından kayıtları yükle
        self.kayitlari_yukle()

    def kaydet(self):
        tc = self.tc_input.text()
        ad = self.ad_input.text()
        soyad = self.soyad_input.text()
        dogum_tarihi = self.dogum_tarihi_input.text()

        if not tc or not ad or not soyad or not dogum_tarihi:
            QMessageBox.warning(self, 'Uyarı', 'Lütfen tüm alanları doldurun!')
            return

        try:
            # Doğum tarihini kontrol et
            QDate.fromString(dogum_tarihi, 'dd/MM/yyyy')
        except:
            QMessageBox.warning(self, 'Uyarı', 'Geçersiz doğum tarihi formatı! Lütfen GG/AA/YYYY formatında girin.')
            return

        # CSV dosyasına kaydet
        with open('personel_kayitlari.csv', 'a', newline='', encoding='utf-8') as file:
            writer = csv.writer(file)
            writer.writerow([tc, ad, soyad, dogum_tarihi])

        QMessageBox.information(self, 'Başarılı', 'Kayıt başarıyla eklendi!')

        # Input alanlarını temizle
        self.tc_input.clear()
        self.ad_input.clear()
        self.soyad_input.clear()
        self.dogum_tarihi_input.clear()

        # Tabloyu güncelle
        self.kayitlari_yukle()

    def kayitlari_yukle(self):
        # CSV dosyasından kayıtları oku ve tabloya yükle
        try:
            with open('personel_kayitlari.csv', 'r', newline='', encoding='utf-8') as file:
                reader = csv.reader(file)
                kayitlar = list(reader)

                # Tabloyu temizle
                self.tablo.setRowCount(0)

                # Kayıtları tabloya ekle
                for row_number, row_data in enumerate(kayitlar):
                    self.tablo.insertRow(row_number)
                    for column_number, data in enumerate(row_data):
                        self.tablo.setItem(row_number, column_number, QTableWidgetItem(data))
        except FileNotFoundError:
            # Dosya yoksa, boş bir tablo göster
            self.tablo.setRowCount(0)

if __name__ == '__main__':
    app = QApplication(sys.argv)
    pencere = PersonelKayitSistemi()
    pencere.show()
    sys.exit(app.exec_())
```
