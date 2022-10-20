FEATURES:

1- https://www.youtube.com/watch?v=2mGKRYtXqvo

LINK :

1- https://www.youtube.com/watch?v=gFxZ3SerLoo

2- https://learndataanalysis.org/developing-a-desktop-app-to-merge-pdf-files-with-python-and-pyqt5-full-tutorial/

3- https://www.youtube.com/watch?v=NLQ_xSAs2DY

INFORMATION : 

⚠ **Before diving into development, makes sure you install PyQt5** ( _pip install PyQt5_ ) **and Pyinstaller libraries** ( _pip install pyinstaller_ )

**⚠  To convert .py to .exe, use this code after opening in powershell** : 

>  pyinstaller --onefile Name.py


STEPS : 

- [ ] Find libraries suitable for my project
- [ ] Write the code without using GUI
- [ ] Implement GUI


DESIGN : 

- [ ] Simple
- [ ] Intuitive
- [ ] Functional


METHOD : 

18/10/2022
I searched online for various ways to solve my problem, unfortunately the best way to my goal is to develop it in python which with the right libraries makes it great for my work.
So before I relied on building it in C ++, I started building it in the Python language.
I found a site that explains the topic very well and in depth by also creating a graphical interface around the source code. I had to do several tests for the GUI and for the correct resizing of the interface.
Finally I found a detailed guide to transform the .py file into an executable then .exe, Below you will find the link to both the source code and the .exe.
Later my goal is to integrate the spliiter and maybe do it in C # / C / C ++ language.

20/10/2022
after two days spent refining this project, I announce that I have finally finished splitting the PDF files, now the design is divided in half vertically where on the left you can see the MERGE tool, while on the right you find the SPLITTER tool.
The code below is already updated to the latest version

DRIVE LINKS :

**OLD DOWNLOADS**

1- https://drive.google.com/file/d/13jD8hZz0xw9CFtGMeh0GNUd06bXoxs7U/view?usp=sharing

2- https://drive.google.com/file/d/1Kxy1g_l-J-yvFcGfy0KHDGImU4zdTY8d/view?usp=sharing

3- https://drive.google.com/file/d/1jh5njp1YTe4uCEoojbVK38DmRa57Yv5P/view?usp=sharing

**NEW DONLOADS**

1- https://drive.google.com/file/d/1e0RibipP5yxpD-O8vmvx0ey6OBOhWOU3/view?usp=sharing

2- https://drive.google.com/file/d/1A77g1q-jIMukg0Nh1g-gEckoPRZT-cL1/view?usp=sharing

3- https://drive.google.com/file/d/1EFVlUDfr9SULUO0mOLYfPQ3xNgSF43RN/view?usp=sharing

SOURCE CODE :


```
import sys, os, io
if hasattr(sys, 'frozen'):
    os.environ['PATH'] = sys._MEIPASS + ";" + os.environ['PATH']
from PyQt5.QtWidgets import QApplication, QWidget, QLabel, QLineEdit, QPushButton, QListWidget, \
                            QVBoxLayout, QHBoxLayout, QGridLayout, \
                            QDialog, QFileDialog, QMessageBox, QAbstractItemView
from PyQt5.QtCore import Qt, QUrl
from PyQt5.QtGui import QIcon
from PyPDF2 import PdfFileMerger, PdfFileWriter, PdfFileReader




def resource_path(relative_path):
    try:
        base_path = sys._MEIPASS
    except Exception:
        base_path = os.path.abspath(".")

    return os.path.join(base_path, relative_path)   

class ListWidget(QListWidget):
    def __init__(self, parent=None):
        super().__init__(parent=None)
        self.setAcceptDrops(True)
        self.setStyleSheet('''font-size:15px''')
        self.setDragDropMode(QAbstractItemView.InternalMove)
        self.setSelectionMode(QAbstractItemView.ExtendedSelection)

    def dragEnterEvent(self, event):
        if event.mimeData().hasUrls():
            event.accept()
        else:
            return super().dragEnterEvent(event) # return to the original event state

    def dragMoveEvent(self, event):
        if event.mimeData().hasUrls():
            event.setDropAction(Qt.CopyAction)
            event.accept()
        else:
            return super().dragMoveEvent(event) # return to the original event state

    def dropEvent(self, event):
        if event.mimeData().hasUrls():
            event.setDropAction(Qt.CopyAction)
            event.accept()

            pdfFiles = []

            for url in event.mimeData().urls():
                if url.isLocalFile():
                    if url.toString().endswith('.pdf'):
                        pdfFiles.append(str(url.toLocalFile()))
            self.addItems(pdfFiles)
        else:
            return super().dropEvent(event) # return to the original event state

class output_field(QLineEdit):
    def __init__(self):
        super().__init__()          
        # self.setMinimumHeight(50)
        self.height = 25
        self.setStyleSheet('''font-size: 15px;''')

        self.setFixedHeight(self.height)

    def dragEnterEvent(self, event):
        if event.mimeData().hasUrls:
            event.accept()
        else:
            event.ignore()

    def dragMoveEvent(self, event):
        if event.mimeData().hasUrls:
            event.setDropAction(Qt.CopyAction)
            event.accept()
        else:
            event.ignore()

    def dropEvent(self, event):
        if event.mimeData().hasUrls:
            event.setDropAction(Qt.CopyAction)
            event.accept()

            if event.mimeData().urls():
                self.setText(event.mimeData().urls()[0].toLocalFile())                                  
        else:
            event.ignore()


class button(QPushButton):
    def __init__(self, label_text):
        super().__init__()
        self.setText(label_text)
        self.setStyleSheet('''
            font-size: 15px;
            width: 117px;
            height: 20;
        ''')


class AppDemo(QWidget):
    def __init__(self):
        super().__init__()
        self.setWindowTitle('PDF Files Merge Utility')
        self.setWindowIcon(QIcon(resource_path(r'PDF.ico')))
        self.resize(800, 600)
        self.initUI()

        self.buttonBrowseOutputFile.clicked.connect(self.populateFileName)
        self.buttonSplitBrowseOutputFile.clicked.connect(self.splitFileName)

    def initUI(self):
        generalLayout = QHBoxLayout()
        mergeLayout = QVBoxLayout()
        splitLayout=QVBoxLayout()
        outputFolderRow = QHBoxLayout()
        outputSplitRow = QHBoxLayout()
        buttonLayout = QHBoxLayout()


        self.outputFile = output_field()
        self.outputsplitFile = output_field()
        outputFolderRow.addWidget(self.outputFile)
        outputSplitRow.addWidget(self.outputsplitFile)

        # browse button
        self.buttonBrowseOutputFile = button('&Save To')
        self.buttonBrowseOutputFile.setFixedHeight(self.outputFile.height)
        outputFolderRow.addWidget(self.buttonBrowseOutputFile)
        self.buttonSplitBrowseOutputFile = button('&Import')
        self.buttonSplitBrowseOutputFile.setFixedHeight(self.outputFile.height)

#        self.buttonSplitBrowseOutputFile.setFixedHeight(self.splitFile.height)        
        outputSplitRow.addWidget(self.buttonSplitBrowseOutputFile)
        
        

        self.pdfListWidget = ListWidget(self)
        self.pdfsplitwidget = ListWidget(self)



        """
        Buttons
        """
        self.buttonDeleteSelect = button('&Delete')
        self.buttonDeleteSelect.clicked.connect(self.deleteSelected)
        buttonLayout.addWidget(self.buttonDeleteSelect, 1, Qt.AlignRight)

        self.buttonMerge = button('&Merge')
        # buttonMerge.setIcon(QIcon('./Icons/play.ico'))
        self.buttonMerge.clicked.connect(self.mergeFile)
        buttonLayout.addWidget(self.buttonMerge)

    #    self.buttonClose = button('&Close')
    #    self.buttonClose.clicked.connect(QApplication.quit)
    #    buttonLayout.addWidget(self.buttonClose)

        # reset button
        self.buttonReset = button('&Reset')
        self.buttonReset.clicked.connect(self.clearQueue)
        buttonLayout.addWidget(self.buttonReset)

        # split button

        self.splitButton = button('&Split')
        self.splitButton.clicked.connect(self.splitFile)
        #buttonLayout.addWidget(self.splitButton)

        mergeLayout.addLayout(outputFolderRow)
        mergeLayout.addWidget(self.pdfListWidget)
        mergeLayout.addLayout(buttonLayout)


        splitLayout.addLayout(outputSplitRow)
        splitLayout.addWidget(self.pdfsplitwidget)
        splitLayout.addWidget(self.splitButton)


        generalLayout.addLayout(mergeLayout)
        generalLayout.addLayout(splitLayout)

        self.setLayout(generalLayout)

    def deleteSelected(self):
        for item in self.pdfListWidget.selectedItems():
            self.pdfListWidget.takeItem(self.pdfListWidget.row(item))

    def clearQueue(self):
        self.pdfListWidget.clear()
        self.outputFile.setText('')

    def populateFileName(self):
        path = self._getSaveFilePath()
        if path:
            self.outputFile.setText(path)

    def splitFileName(self):
        path = self._getSplitFilePath()
        
    def dialogMessage(self, message):
        dlg = QMessageBox(self)
        dlg.setWindowTitle('PDF Manager')
        dlg.setIcon(QMessageBox.Information)
        dlg.setText(message)
        dlg.show()

    def _getSaveFilePath(self):
        file_save_path, _ = QFileDialog.getSaveFileName(self, 'Save PDF file', os.getcwd(), 'PDF file (*.pdf)') 
        return file_save_path

    def _getSplitFilePath(self):
        global file_save_split_path
        file_save_split_path, _ = QFileDialog.getOpenFileName(self, 'Save PDF file', os.getcwd(), 'PDF file (*.pdf)') 
        self.outputsplitFile.setText(file_save_split_path)
        return file_save_split_path 

    def mergeFile(self):
        if not self.outputFile.text():
            # self.dialogMessage('Save File directory is empty')    
            self.populateFileName()
            return

        if self.pdfListWidget.count() > 0:
            pdfMerger = PdfFileMerger()
            try:                                
                for i in range(self.pdfListWidget.count()):
                    # print(self.pdfListWidget.item(i).text())
                    pdfMerger.append(self.pdfListWidget.item(i).text())

                pdfMerger.write(self.outputFile.text())
                pdfMerger.close()

                #self.pdfListWidget.clear()
                self.dialogMessage('PDF merge complete.')

            except Exception as e:
                self.dialogMessage(e)
        else:
            self.dialogMessage('Queue is empty')

    def splitFile(self):
        
        inputpdf = PdfFileReader(open(file_save_split_path, "rb"))
            
        for i in range(inputpdf.numPages):
            
            output = PdfFileWriter()
            output.addPage(inputpdf.getPage(i))
            with open("document-page%s.pdf" % i, "wb") as outputStream:
                output.write(outputStream)
                self.pdfsplitwidget.addItem("document-page%s.pdf" % i)
        self.dialogMessage('PDF split complete.')

                    
                    

        
            

if __name__ == '__main__':
    app = QApplication(sys.argv)
    app.setStyle("fusion")
    app.setAttribute(Qt.AA_EnableHighDpiScaling, True)


    demo = AppDemo()
    demo.show()

    sys.exit(app.exec_())
```
