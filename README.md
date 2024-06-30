# app-para-analise-de-dados
import sys
from PyQt5.QtWidgets import QApplication, QMainWindow, QWidget, QVBoxLayout, QPushButton, QLabel, QFileDialog
from PyQt5.QtGui import QPixmap
import openpyxl
import pandas as pd
import matplotlib.pyplot as plt
from io import BytesIO

class DataAnalyzerApp(QMainWindow):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('Data Analyzer')
        self.setGeometry(200, 200, 400,400 )

        self.central_widget = QWidget()
        self.setCentralWidget(self.central_widget)

        self.layout = QVBoxLayout()
        self.central_widget.setLayout(self.layout)

        self.btn_load = QPushButton('Carregar Arquivo XLSX', self)
        self.btn_load.clicked.connect(self.load_xlsx)
        self.layout.addWidget(self.btn_load)

        self.btn_analyze = QPushButton('Analisar Dados', self)
        self.btn_analyze.clicked.connect(self.analyze_data)
        self.layout.addWidget(self.btn_analyze)

        self.label_status = QLabel('')
        self.layout.addWidget(self.label_status)

        self.label_image = QLabel(self)
        self.layout.addWidget(self.label_image)

        self.file_path = ''

    def load_xlsx(self):
        options = QFileDialog.Options()
        file_name, _ = QFileDialog.getOpenFileName(self, "Escolha um arquivo XLSX", "", "XLSX Files (*.xlsx)", options=options)
        if file_name:
            self.file_path = file_name
            self.label_status.setText(f'Arquivo carregado: {file_name}')

    def analyze_data(self):
        if self.file_path:
            try:
                df = pd.read_excel(self.file_path)
                # Aqui você colocaria sua lógica de análise dos dados
                # Vamos supor que você queira criar um gráfico simples de exemplo
                plt.bar(df['Produto'], df['Quantidade'])
                plt.title('Quantidade por Produto')
                plt.xlabel('Produto')
                plt.ylabel('Quantidade')
                plt.xticks(rotation=15)
                buf = BytesIO()
                plt.savefig(buf, format='png')
                buf.seek(0)
                pixmap = QPixmap()
                pixmap.loadFromData(buf.getvalue())
                self.label_image.setPixmap(pixmap)
                buf.close()
            except Exception as e:
                self.label_status.setText(f'Erro ao analisar dados: {str(e)}')
        else:
            self.label_status.setText('Carregue um arquivo XLSX primeiro.')

if __name__ == '__main__':
    app = QApplication(sys.argv)
    window = DataAnalyzerApp()
    window.show()
    sys.exit(app.exec_())
