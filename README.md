#Ejercicio de 2 ventanas

import sys
from PyQt5.QtWidgets import (QApplication, QWidget, QLabel, QLineEdit, QGridLayout, QPushButton, QVBoxLayout, QMessageBox, QDateEdit)
from PyQt5.QtGui import QFont
from PyQt5.QtCore import Qt, QDate

class VentanaFormulario(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Afiliados - Chacarita Juniors")
        self.setGeometry(100, 100, 500, 350)
        self.setStyleSheet("background-color: #add8e6;")  # celeste

        layout = QGridLayout()
        self.setLayout(layout)

        # Ejercicio 1: Crear la ventana de contexto
        # Título del formulario
        titulo = QLabel("Formulario de Afiliación")
        titulo.setFont(QFont('Arial', 18, QFont.Bold))
        titulo.setAlignment(Qt.AlignCenter)
        layout.addWidget(titulo, 0, 0, 1, 2)
        
        
        # Widgets para los datos del afiliado
        lbl_nombre = QLabel("Nombre:")
        self.txt_nombre = QLineEdit()
        lbl_apellido = QLabel("Apellido:")
        self.txt_apellido = QLineEdit()
        lbl_dni = QLabel("DNI:")
        self.txt_dni = QLineEdit()
        lbl_nacimiento = QLabel("Fecha de nacimiento:")
        self.date_nacimiento = QDateEdit(QDate.currentDate())
        self.date_nacimiento.setCalendarPopup(True)
        self.date_nacimiento.setDisplayFormat("dd/MM/yyyy")
        self.date_nacimiento.setMaximumDate(QDate.currentDate())
        
        layout.addWidget(lbl_nombre, 1, 0)
        layout.addWidget(self.txt_nombre, 1, 1)
        layout.addWidget(lbl_apellido, 2, 0)
        layout.addWidget(self.txt_apellido, 2, 1)
        layout.addWidget(lbl_dni, 3, 0)
        layout.addWidget(self.txt_dni, 3, 1)
        layout.addWidget(lbl_nacimiento, 4, 0)
        layout.addWidget(self.date_nacimiento, 4, 1)

class VentanaHerramientas(QWidget):
    def __init__(self, formulario_ref):
        super().__init__()
        self.setWindowTitle("Herramientas")
        self.setGeometry(650, 100, 200, 300)
        self.setStyleSheet('color: blanco')
        
        # Guardar la referencia a la ventana del formulario
        self.formulario_ref = formulario_ref
        
        layout = QVBoxLayout()
        self.setLayout(layout)
        
        
        # Ejercicio 2
        
        btn_guardar = QPushButton("Guardar")
        btn_abrir = QPushButton("Abrir")
        btn_buscar = QPushButton("Buscar")
        btn_salir = QPushButton("Salir")
        
        layout.addWidget(btn_guardar)
        layout.addWidget(btn_abrir)
        layout.addWidget(btn_buscar)
        layout.addStretch() # Espaciador para empujar el botón Salir hacia abajo
        layout.addWidget(btn_salir)

        # Ejercicio 4
        btn_guardar.clicked.connect(self.guardar_datos)
        btn_salir.clicked.connect(self.cerrar_aplicacion)
        
    def guardar_datos(self):
        nombre = self.formulario_ref.txt_nombre.text().strip()
        apellido = self.formulario_ref.txt_apellido.text().strip()
        dni = self.formulario_ref.txt_dni.text().strip()
        
        if not nombre or not apellido or not dni:
            QMessageBox.warning(self, "Error", "Por favor, complete todos los campos obligatorios.")
            return
        # Mostrar un resumen de los datos ingresados

        resumen = f"""
        Datos Guardados:
        Nombre: {nombre}
        Apellido: {apellido}
        DNI: {dni}
        Fecha de Nacimiento: {self.formulario_ref.date_nacimiento.date().toString(Qt.DefaultLocaleShortDate)}
        """
        QMessageBox.information(self, "Guardado Exitoso", resumen)

    def cerrar_aplicacion(self):
       QApplication.instance().quit()

    
if __name__ == '__main__':
    app = QApplication(sys.argv)
    
    # Ejercicio 3: Mostrar ambas ventanas a la vez
    ventana_form = VentanaFormulario()
    ventana_herr = VentanaHerramientas(ventana_form) # Pasar la referencia
    
    ventana_form.show()
    ventana_herr.show()
    
    sys.exit(app.exec_())
