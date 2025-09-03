import sys
from PyQt5.QtWidgets import (
    QApplication, QWidget, QLabel, QLineEdit, QGridLayout,
    QVBoxLayout, QDateEdit, QPushButton, QMessageBox, QInputDialog
)
from PyQt5.QtGui import QFont, QPixmap
from PyQt5.QtCore import Qt, QDate


class VentanaFormulario(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle("Afiliados - Chacarita Juniors")
        self.setGeometry(100, 100, 500, 500)

        layout_principal = QVBoxLayout(self)

        # ---- Título ----
        titulo = QLabel("FORMULARIO DE AFILIACIÓN")
        titulo.setFont(QFont('Arial', 22, QFont.Bold))
        titulo.setAlignment(Qt.AlignCenter)
        titulo.setStyleSheet("color: black;")
        layout_principal.addWidget(titulo)

        # ---- Imagen debajo del título ----
        imagen = QLabel()
        pixmap = QPixmap("fondoblanco.png")
        imagen.setPixmap(pixmap.scaledToWidth(300, Qt.SmoothTransformation))
        imagen.setAlignment(Qt.AlignCenter)
        layout_principal.addWidget(imagen)

        # ---- Formulario ----
        grid = QGridLayout()

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

        grid.addWidget(lbl_nombre, 0, 0)
        grid.addWidget(self.txt_nombre, 0, 1)
        grid.addWidget(lbl_apellido, 1, 0)
        grid.addWidget(self.txt_apellido, 1, 1)
        grid.addWidget(lbl_dni, 2, 0)
        grid.addWidget(self.txt_dni, 2, 1)
        grid.addWidget(lbl_nacimiento, 3, 0)
        grid.addWidget(self.date_nacimiento, 3, 1)

        layout_principal.addLayout(grid)


class VentanaHerramientas(QWidget):
    def __init__(self, formulario_ref):
        super().__init__()
        self.setWindowTitle("Herramientas")
        self.setGeometry(650, 100, 200, 300)

        # Fondo y colores
        self.setStyleSheet("""
            QWidget {
                background-color: white;   /* fondo blanco */
                color: black;              /* texto negro */
            }
            QPushButton {
                background-color: #4682B4; /* botones azul acero */
                color: white;              /* texto blanco en botones */
                font-weight: bold;
                border-radius: 8px;
                padding: 6px;
            }
            QPushButton:hover {
                background-color: #5A9BD6; /* azul más claro al pasar el mouse */
            }
        """)

        # Guardar referencia a la ventana del formulario
        self.formulario_ref = formulario_ref

        layout = QVBoxLayout()
        self.setLayout(layout)

        # ---- Botones ----
        btn_guardar = QPushButton("Guardar")
        btn_abrir = QPushButton("Abrir")
        btn_buscar = QPushButton("Buscar")
        btn_salir = QPushButton("Salir")

        layout.addWidget(btn_guardar)
        layout.addWidget(btn_abrir)
        layout.addWidget(btn_buscar)
        layout.addStretch()
        layout.addWidget(btn_salir)

        # Conexiones
        btn_guardar.clicked.connect(self.guardar_datos)
        btn_abrir.clicked.connect(self.abrir_datos)
        btn_buscar.clicked.connect(self.buscar_datos)
        btn_salir.clicked.connect(self.cerrar_aplicacion)

    def guardar_datos(self):
        nombre = self.formulario_ref.txt_nombre.text().strip()
        apellido = self.formulario_ref.txt_apellido.text().strip()
        dni = self.formulario_ref.txt_dni.text().strip()
        nacimiento = self.formulario_ref.date_nacimiento.date().toString("dd/MM/yyyy")

        if not nombre or not apellido or not dni:
            QMessageBox.warning(self, "Error", "Por favor, complete todos los campos obligatorios.")
            return

        with open("afiliados.txt", "a", encoding="utf-8") as f:
            f.write(f"{nombre},{apellido},{dni},{nacimiento}\n")

        QMessageBox.information(self, "Guardado Exitoso", "El afiliado fue guardado correctamente.")

    def abrir_datos(self):
        nombre = self.formulario_ref.txt_nombre.text().strip()
        apellido = self.formulario_ref.txt_apellido.text().strip()
        dni = self.formulario_ref.txt_dni.text().strip()
        nacimiento = self.formulario_ref.date_nacimiento.date().toString("dd/MM/yyyy")

        if not nombre and not apellido and not dni:
            QMessageBox.information(self, "Formulario vacío", "No hay datos cargados en el formulario.")
        else:
            QMessageBox.information(
                self,
                "Datos actuales",
                f"Nombre: {nombre}\nApellido: {apellido}\nDNI: {dni}\nNacimiento: {nacimiento}"
            )

    def buscar_datos(self):
        dni_buscar, ok = QInputDialog.getText(self, "Buscar Afiliado", "Ingrese DNI:")
        if ok and dni_buscar:
            try:
                with open("afiliados.txt", "r", encoding="utf-8") as f:
                    for linea in f:
                        nombre, apellido, dni, nacimiento = linea.strip().split(",")
                        if dni == dni_buscar:
                            QMessageBox.information(
                                self,
                                "Afiliado encontrado",
                                f"Nombre: {nombre}\nApellido: {apellido}\nNacimiento: {nacimiento}"
                            )
                            return
                QMessageBox.warning(self, "No encontrado", "No existe un afiliado con ese DNI.")
            except FileNotFoundError:
                QMessageBox.critical(self, "Error", "No existe el archivo de afiliados.")

    def cerrar_aplicacion(self):
        QApplication.instance().quit()


if __name__ == '__main__':
    app = QApplication(sys.argv)

    ventana_form = VentanaFormulario()
    ventana_herr = VentanaHerramientas(ventana_form)

    ventana_form.show()
    ventana_herr.show()

    sys.exit(app.exec_())

