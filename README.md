#importing modules and widgets
from PyQt5.QtCore import Qt
from PyQt5.QtWidgets import QApplication, QWidget, QVBoxLayout, QPushButton, QHBoxLayout, QLabel, QLineEdit, QListWidget, QMessageBox
import json
#declaring constants
win_width, win_height = 200, 300
win_x, win_y = 200, 200
txt_title = "List of questions"
txt_line = "Entry field"

class MainWindow(QWidget):
    def __init__(self, parent=None, flags=Qt.WindowFlags()):
        super().__init__(parent=parent, flags=flags)
        # creating and customizing the graphical elements:
        self.initUI()
        self.read_json()
        #connects the elements
        self.connects()
        #determines how the window will look (text, size, location)
        self.set_appear()
        self.questions_dict = dict()
        # start:
        self.show()

    def read_json(self):
        try:
            with open('sample.json', 'r') as openfile:
                self.questions_dict = json.load(openfile)
                self.add_all_questions()
        except:
            print("nothing to read")

    def initUI(self):
        ''' creates graphical elements '''
        self.btn_question = QPushButton("New question" ,self)
        self.btn_delete = QPushButton("Delete question" ,self)
        self.btn_begin = QPushButton("Begin practice" ,self)
        self.label_question = QLabel("Question:")
        self.label_answer = QLabel("Answer:")
        self.label_option1 = QLabel("Wrong answer option 1:")
        self.label_option2 = QLabel("Wrong answer option 2:")
        self.label_option3 = QLabel("Wrong answer option 3:")

        self.line_question = QLineEdit()
        self.line_answer = QLineEdit()
        self.line_option1 = QLineEdit()
        self.line_option2 = QLineEdit()
        self.line_option3 = QLineEdit()

        layoutH1 = QHBoxLayout()
        layoutH1.addWidget(self.btn_begin, alignment = Qt.AlignCenter)

        layoutH2 = QHBoxLayout()
        layoutH2.addWidget(self.btn_question, alignment = Qt.AlignLeft)
        layoutH2.addWidget(self.btn_delete, alignment = Qt.AlignRight)

        layoutV1 = QVBoxLayout()
        layoutV1.addWidget(self.label_question ,alignment = Qt.AlignRight)
        layoutV1.addWidget(self.label_answer ,alignment = Qt.AlignRight)
        layoutV1.addWidget(self.label_option1 ,alignment = Qt.AlignRight)
        layoutV1.addWidget(self.label_option2 ,alignment = Qt.AlignRight)
        layoutV1.addWidget(self.label_option3 ,alignment = Qt.AlignRight)

        layoutV2 = QVBoxLayout()
        layoutV2.addWidget(self.line_question ,alignment = Qt.AlignLeft)
        layoutV2.addWidget(self.line_answer ,alignment = Qt.AlignLeft)
        layoutV2.addWidget(self.line_option1 ,alignment = Qt.AlignLeft)
        layoutV2.addWidget(self.line_option2 ,alignment = Qt.AlignLeft)
        layoutV2.addWidget(self.line_option3 ,alignment = Qt.AlignLeft)

        layoutH3 = QHBoxLayout()
        self.list = QListWidget()

        self.list.setMinimumWidth(120)
        self.list.setMinimumHeight(80)
        layoutH3.addWidget(self.list)
        layoutH3.addLayout(layoutV1)
        layoutH3.addLayout(layoutV2)

        self.layout_main = QVBoxLayout()
        
        self.layout_main.addLayout(layoutH3)
        
        self.layout_main.addLayout(layoutH2)
        self.layout_main.addLayout(layoutH1)
        self.layout_main.addLayout(layoutV1)
        
        self.setLayout(self.layout_main)

    def set_appear(self):
        self.setWindowTitle(txt_title)
        self.resize(win_width, win_height)
        self.move(win_x, win_y)

    def new_question(self):
        question = self.line_question.text()
        answer = self.line_answer.text()
        option1 = self.line_option1.text()
        option2 = self.line_option2.text()
        option3 = self.line_option3.text()

        # print("question",question)
        # print("answer",answer)
        # print("option1",option1)
        # print("option2",option2)
        # print("option3",option3)
        msgBox = QMessageBox()
        msgBox.setText("Question empty")
        msgBox.setWindowTitle("Empty")
        # msgBox.setStandardButton(QMessageBox.Empty)

        if question == "":
            msgBox.setText("Question empty")
            x = msgBox.exec_() 
        elif answer == "":
            msgBox.setText("Answer empty")
            x = msgBox.exec_()
        elif option1 == "":
            msgBox.setText("Option 1 empty")
            x = msgBox.exec_()
        elif option2 == "":
            msgBox.setText("Option 2 empty")
            x = msgBox.exec_()
        elif option3 == "":
            msgBox.setText("Option 3 empty")
            x = msgBox.exec_()
        else:
            self.questions_dict[question] = {"answer":answer , "wrong_1":option1 , "wrong_2":option2 , "wrong_3":option3}
            print(self.questions_dict)
            json_object = json.dumps(self.questions_dict ,indent=4)
            print(json_object)
            with open("sample.json", "w") as outfile:
                outfile.write(json_object)

            self.add_all_questions()

    
    def delete_question(self):
        current_item = self.list.currentItem()

        if current_item:
            question_to_delete = current_item.text()
            del self.questions_dict[question_to_delete]

            

            self.add_all_questions()
        self.btn_delete.clicked.connect(self.delete_question)
    def begin_learn(self):
        pass

    #functie care adauga toate cheile din dictionar in lista:
    def add_all_questions(self):
        self.list.clear()
        questions = list(self.questions_dict.keys())
        self.list.addItems(questions)

    #functie care sterge elementele:
    def clear_list(self):
        self.list.clear()



    def connects(self):
        self.btn_question.clicked.connect(self.new_question)
        self.btn_delete.clicked.connect(self.delete_question)
        self.btn_begin.clicked.connect(self.begin_learn)

app = QApplication([])
mw = MainWindow()
app.exec_()
