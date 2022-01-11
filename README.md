# Todo-App-with-MVC-version-final

**Notes:**

Please To login use wafae as a username , and 123456 as a password .

## Introduction:
Todo list is an application for organization of a list of tasks you need to complete, or things that you want to do in order of Date (and may be in order of priority). The first part of the project will focus on the core features and skeleton of the application . In part two we’ll take care of text-formatting ( like add, remove, save, clear... ). And in part three, we’ll add some useful extensions like login and find dialog.

**This is what it’ll look like at the end of this project**


Before we get started, we need to add resources files for icons.

### Resource Files
Todo app uses several icons to represent various actions. The icons are in the resources directory which is directly under the TextEditor project directory.

To register the image files, in the Edit mode, right-click the icons.qrc file and select Open in Editor.

Click the Add button and select Add Files.

In the file manager, select the files to be added.

## Part One:

 Todo Application is in the MainWindow class, which inherits QMainWindow. QMainWindow provides the framework for windows that have menus, toolbars, dock windows, and a status bar. 
 
 The application provides File, Options, Window and Help entries in the menu bar, with the following Actions:
 
![image](https://user-images.githubusercontent.com/75392302/147761593-7fd483f2-85a2-42bc-ac1f-9b5290d4aeac.png)
![image](https://user-images.githubusercontent.com/75392302/147761652-439ea387-aaf9-48b6-873c-398f96b6ab0b.png)
![image](https://user-images.githubusercontent.com/75392302/147761717-341effc9-48bb-4f63-8992-e6c29bd34496.png)
![image](https://user-images.githubusercontent.com/75392302/147761772-c13378eb-d46d-4f05-b5d3-201d64f583f2.png)

### Widgets
Todo app contains three QListWidgets , one for Today tasks, an other for Pending tasks, and the last one for completed tasks.
we will need three Qlables for descripe the category of those three lists . Also we will need three Layouts to organise the previous widgets and of corse we need a main widget where we will add all those components.

```cpp
 QWidget* pWidget = new QWidget(this);
        pWidget->setStyleSheet("background-color: #ECF0F1");
        setCentralWidget(pWidget);

        QVBoxLayout* pMainLayout = new QVBoxLayout();
        pWidget->setLayout(pMainLayout);

        QLabel* pwTitle = new QLabel("To Do List", this);
        pMainLayout->addWidget(pwTitle);
        pwTitle->setAlignment(Qt::AlignCenter);
        pwTitle->setStyleSheet("font-size: 30pt; margin: 10%;");

        QHBoxLayout* pHLayoutLabels = new QHBoxLayout();
        pMainLayout->addLayout(pHLayoutLabels);

        QLabel* plblToday = new QLabel("ToDay", this);
        plblToday->setStyleSheet("font-size: 15pt;");
        pHLayoutLabels->addWidget(plblToday);

        QLabel* plblPending = new QLabel("Pending", this);
        plblPending->setStyleSheet("font-size: 15pt;");
        pHLayoutLabels->addWidget(plblPending);


        QHBoxLayout* pHLayout = new QHBoxLayout();
        QVBoxLayout* pVLayout = new QVBoxLayout();
        pMainLayout->addLayout(pHLayout);
        pMainLayout->addLayout(pVLayout);

        QLabel* plblCompleted = new QLabel("Completed", this);
        plblCompleted->setStyleSheet("font-size: 15pt;");
        pVLayout->addWidget(plblCompleted);


        todayList = new QListWidget(this);
        todayList->setDragEnabled(true);
        todayList->setAcceptDrops(true);
        todayList->setDropIndicatorShown(true);
        todayList->setDefaultDropAction(Qt::MoveAction);
        pHLayout->addWidget(todayList);

        pendingList = new QListWidget(this);
        pendingList->setDragEnabled(true);
        pendingList->setAcceptDrops(true);
        pendingList->setDropIndicatorShown(true);
        pendingList->setDefaultDropAction(Qt::MoveAction);
       pHLayout->addWidget(pendingList);

        completedList = new QListWidget(this);
        completedList->setDragEnabled(true);
        completedList->setAcceptDrops(true);
        completedList->setDropIndicatorShown(true);
        completedList->setDefaultDropAction(Qt::MoveAction);
        pVLayout->addWidget(completedList);

        pendingList->setStyleSheet
        ("QListWidget { font-size: 10pt; font-weight: bold; }");

        completedList->setStyleSheet
        ("QListWidget { font-size: 10pt; font-weight: bold; }");

        todayList->setStyleSheet
        ("QListWidget { font-size: 10pt; font-weight: bold; }");
```
As you can see we use setStyleSheet() function to set style for my lists . 
Also setDefaultDropAction() function that holds the drop action .

### Actions:
In the MainWindow constructor, we create Actions of my application .

For all the actions that we’ll be creating follow the same code pattern:

Create a QAction and pass it an icon and a name

Connect the QAction’s triggered signal to a slot function.

**Add :** Action to create a new Task. 
```cpp
    // New task
  AddAction = new QAction("Add Task",this);
  AddAction->setIcon(QIcon(":/add.jfif"));
  connect(AddAction, &QAction::triggered, this, &MainWindow::Add);
```

**remove :** Action to delete an added Task.
```cpp
    // remove task
  RemoveAction = new QAction("Delete",this);
  RemoveAction->setIcon(QIcon(":/delete.jfif"));
  connect(RemoveAction, &QAction::triggered, this, &MainWindow::RemoveTask);
```

**Save :** Action to save Tasks in a txt file .
```cpp
   saveAction = new QAction("Save",this);
   saveAction->setIcon(QIcon(":/save.gif"));
   connect(saveAction, &QAction::triggered, this, &MainWindow::save);
```

**close :** Action to close the application .
```cpp
  closeAction = new QAction("Close",this);
  closeAction->setIcon(QIcon(":/download.jfif"));
  connect(closeAction, &QAction::triggered, this, &MainWindow::close);
```

**clear :** Action to delete all added Tasks for the three lists.
```cpp
   clearAction = new QAction("Clear",this);
   clearAction->setIcon(QIcon(":/clear.jpg"));
   connect(clearAction, &QAction::triggered, this, &MainWindow::clear);

```

**Find :** Action to find an added Task.
```cpp
   findAction = new QAction("Find",this);
   findAction->setIcon(QIcon(":/find.png"));
   connect(findAction, &QAction::triggered, this, &MainWindow::find);

```

**Minimise/Maximise :** Action to Minimise/Maximise the window .
```cpp
 minimiseAction = new QAction("Minimise",this);
 minimiseAction->setIcon(QIcon(":/mi.jfif"));
 connect(minimiseAction, &QAction::triggered, this, &MainWindow::showMinimized);
 
 maximiseAction = new QAction("Maximise",this);
 maximiseAction->setIcon(QIcon(":/max.png"));
 connect(maximiseAction, &QAction::triggered, this, &MainWindow::showMaximized);
```

**FullScreen :** Action to have the full-screen mode .
```cpp
   Fullscreen = new QAction("Fullscreen",this);
   Fullscreen->setIcon(QIcon(":/full.jfif"));
   connect(Fullscreen, &QAction::triggered, this, &MainWindow::showFullScreen);

```

**Show about :** Action to show some information about the application.
```cpp
  showabout = new QAction("About",this);
  showabout->setIcon(QIcon(":/about.jfif"));
  connect(showabout, &QAction::triggered, this, &MainWindow::About);
```

### Menus:
Now we will create File Options Window and Help Menu
Then we add the previous actions to the appropriate menu.

```cpp
       // File Menu
        FileMenu = menuBar()->addMenu("&File");
        FileMenu->addAction(AddAction);
        FileMenu->addAction(RemoveAction);
        FileMenu->addAction(closeAction);

        // options menu
        OptionsMenu=menuBar()->addMenu("&Options");
        OptionsMenu->addAction(saveAction);
        OptionsMenu->addAction(findAction);
        OptionsMenu->addAction(clearAction);

        // window menu
        WindowMenu = menuBar()->addMenu("&Window");
        WindowMenu->addAction(minimiseAction);
        WindowMenu->addAction(maximiseAction);
        WindowMenu->addAction(Fullscreen);

        // Help menu
        helpMenu = menuBar()->addMenu("&Help");
        helpMenu->addAction(showabout);
```
**ToolBar**

Creating toolbars is very similar to creating menus. The same actions that we put in the menus can be reused in the toolbars. After creating the action, we add it to the toolbar using QToolBar::addAction() function .
```cpp
        QToolBar *pToolBar = new QToolBar(this);
        addToolBar(pToolBar);
        
        pToolBar->addAction(AddAction);
        pToolBar->addAction(RemoveAction);
        pToolBar->addAction(saveAction);
        pToolBar->addAction(closeAction);
        pToolBar->addAction(clearAction);
        pToolBar->addAction(findAction);
```

## Part two:

This part is about functions and slots implimentation 

***NewTask***

When the user wants to create a new Task, we call the private function Add().

when we execute Add() function we got a dialog.

This dialog prompts the user to enter the new taks the date of task, to choose a tag and select if this task is finished or not yet.

 First we Create a Form Class,
 
 Using the designer obtain the following the form:

![image](https://user-images.githubusercontent.com/75392302/147767524-a1c49f7d-a727-4ddf-87c6-7005ee429b0a.png)

And we add  public Getters for the line edit text  ,  date edit value ,combo box text and finally we need a function verify if the checkBox is Checked or not to get the information about the task and in which list it will be added.
```cpp
// Return whether or not 'checkbox' UI element is checked
bool Tasks::isCompleted() const
{
    return ui->checkBox->isChecked();
}

QString Tasks::getDescription() const
{
    return ui->lineEdit->text();
}

QString Tasks::getTag() const
{
    return ui->comboBox->currentText();
}

QString Tasks::getDate() const
{
    return ui->dateEdit->date().toString("ddd MMMM yyyy");;
}
bool Tasks::isToday(){

    auto year = ui->dateEdit->date().year();
    auto month = ui->dateEdit->date().month();
    auto day = ui->dateEdit->date().day();

    time_t now = time(0);
    tm *ltm = localtime(&now);

    if(1900 + ltm->tm_year == year && 1 + ltm->tm_mon == month && ltm->tm_mday == day)
        return true;
    return false;
}

```
So in add() function we just create the dialog , Execute the dialog and store the user response. Then we check if the checkBox is Checked , if yes we add the task in completed list, if not we check if the date is today date if yes we add task to Today list if not we add it to Pending list.

```cpp
//Creating the dialog
        Tasks D;

        //Executing the dialog and storing the user response
        auto reply = D.exec();

        //Checking if the dialog is accepted
        if(reply == Tasks::Accepted)
        {
            auto temp = "Task: "+D.getDescription()+" ,Tg: "+D.getTag()+" ,Due: "+D.getDate();

            if(D.isCompleted()){
                completedList->insertItem(completedList->count(),temp);
            }else if(D.isToday()){
                todayList->insertItem(todayList->count(),temp);
            }else{
                pendingList->insertItem(pendingList->count(),temp);
            }

        }

}
```
**Tests:**

![image](https://user-images.githubusercontent.com/75392302/147775525-b8289995-7345-43ff-a75d-a5c11402fe1a.png)

![image](https://user-images.githubusercontent.com/75392302/147775619-06e3db6b-afe4-44c9-8bb2-5abac4c1174f.png)
![image](https://user-images.githubusercontent.com/75392302/147775692-98751261-1541-4ab1-af1e-f965fd30cbd3.png)
![image](https://user-images.githubusercontent.com/75392302/147776061-09342050-3d9c-4779-b9f2-c2f3b648dac3.png)
![image](https://user-images.githubusercontent.com/75392302/147776105-baaf08cc-efb7-4f86-aa55-24111b062318.png)

Here is the result:

![image](https://user-images.githubusercontent.com/75392302/147776179-1139fe82-724e-443d-b9c0-dff03a848c53.png)


***RemoveTask***

When the user wants to delete a Task, we call the private function remove().

when we execute remove() function we got a dialog.

This dialog prompts the user to enter the taks wanted to delete.

 First we Create a Form Class,
 
 Using the designer obtain the following the form:
 
 ![image](https://user-images.githubusercontent.com/75392302/147769268-5194b58d-1614-47d0-85ae-dbf5f934dc44.png)

And we add a public Getter for the line edit Text to get the task to remove .

```cpp
QString Remove::getRemoved() const{
    return ui->lineEdit->text();
}
```
So in remove() function we just create the dialog , Execute the dialog and store the user response. Then we check wich list contains this task and remove it using delete word.
```cpp
//Creating the dialog
     Remove D;

     //Executing the dialog and storing the user response
     auto reply = D.exec();

     //Checking if the dialog is accepted
     if(reply == Remove::Accepted)
     {
         for(int row=pendingList->count()-1;row>=0;row--){
             if(pendingList->item(row)->text().contains(D.getRemoved())){
                     delete pendingList->item(row);

             }
         }
         for(int row=completedList->count()-1;row>=0;row--){
             if(completedList->item(row)->text().contains(D.getRemoved())){
                     delete completedList->item(row);
             }
         }
         for(int row=todayList->count()-1;row>=0;row--){
             if(todayList->item(row)->text().contains(D.getRemoved())){
                     delete todayList->item(row);
             }
         }
```
**Test:**

![image](https://user-images.githubusercontent.com/75392302/147775309-baf631e5-a8a5-48ba-87d8-16ee6086353c.png)
![image](https://user-images.githubusercontent.com/75392302/147775357-915f3fe4-ae70-4dee-84bf-0668a2cb863f.png)


 ***clear*** 
 
 We just call QListWidget::clear() function.
 ```cpp
void MainWindow::clear()
{
    pendingList->clear();
    completedList->clear();
    todayList->clear();
}
 ```
 **Test:**
 
 ![image](https://user-images.githubusercontent.com/75392302/147775357-915f3fe4-ae70-4dee-84bf-0668a2cb863f.png)
 ![image](https://user-images.githubusercontent.com/75392302/147775446-426258c4-ee09-45de-9ee2-69ebc71c714f.png)

 
 ***Save***
 ```cpp
 void MainWindow::save()
    {

        //Creating a file dialog to choose a file graphically
        auto dialog = new QFileDialog(this);

        //Check if the current file has a name or not
        if(currentFile == "")
        {
           currentFile = dialog->getSaveFileName(this,"choose your file");

           //Update the window title with the file name
           setWindowTitle(currentFile);
        }

       //If we have a name simply save the content
       if( currentFile != ""){

               saveList(pendingList,currentFile);
               saveList(todayList,currentFile);
               saveList(completedList,currentFile);

                      }

    }   
 ```
 Creating a file dialog to choose a file graphically.
 
 Check if the current file has a name or not.
 
 If we have a name simply save the content using saveList() function.
 
 If the user clicks Cancel, the returned file name is empty, and we do nothing.
 
 **saveList()** function store the contenent of a QListWidget in a txt file
 ```cpp
 void MainWindow::saveList(QListWidget *listWidget , QString filename){
    QFile file(filename);
        if (file.open(QIODevice::WriteOnly | QIODevice::Text)) {
            // bind it
            QTextStream stream(&file);
            for (int i = 0; i <listWidget->count(); i++) {
                QListWidgetItem *item = listWidget->item(i);
                stream << item->text() << '\n';
            }

            file.close();
        }
}
 ```
 **Test:**
 
![image](https://user-images.githubusercontent.com/75392302/147774821-5dade876-c7a4-457e-aa16-1eb89c1607e5.png)
![image](https://user-images.githubusercontent.com/75392302/147774975-0a666b38-83d1-432a-8b8d-2e545b72c96c.png)
![image](https://user-images.githubusercontent.com/75392302/147775045-9176c89e-fb31-4437-a36b-1b07936c578f.png)

 
 ***Close***

When the user attempts to close the window, we call the private function Close().
```cpp
void SpreadSheet::close()
{
    saveList(pendingList,currentFile);
    saveList(todayList,currentFile);
    saveList(completedList,currentFile);
    
    auto reply = QMessageBox::question(this, "Exit",
                                       "Do you really want to quit?");
    if(reply == QMessageBox::Yes)
        qApp->exit();
}
```
 As you can see before closing the app we need to srore the contenent of each list in a txt file .
 
 And if I want to start the app again we need to load those file In order to save  what we have already write.
 
 **load implementation:**
 ```cpp
 void MainWindow::loadList(QListWidget *listWidget , QString filename){
    QFile file(filename);
            if (file.open(QIODevice::ReadOnly | QIODevice::Text)) {
                // bind it
                QTextStream in(&file);

                while(!in.atEnd()) {
                    QString line = in.readLine();
                    listWidget->addItem(line);
                }

                file.close();
       }
}
 ```
 
 ***About***
 
 The application's About box is done using one statement, using the QMessageBox::about() static function 
 ```cpp
void MainWindow::About()
      {
          QMessageBox::about(this, "About this app",
                              "This is a simple todo application that manages your tasks , hope you like it ");
       }
 ```
 
 ## Part three:
 
### Find Dialog

This dialog prompts the user for a input and seek Item that contains the entered text.
 
 First we Create a Form Class,
 
 Using the designer obtain the following the form:

![image](https://user-images.githubusercontent.com/75392302/147771845-3557c389-ae8f-4191-b079-fa5781650c4a.png)

And we add a public Getter for the line edit Text to get the cell address:
```cpp
QString Find::find() const
     {
         return ui->lineEdit->text();
     }
```

**Now we are setup to create the interesting connexion between the find_function slot and find actions :**

First we will create the proper slot called goCellSlot to respond to the action trigger.

 ```cpp
 private slots:
 void find();           
 ```
Then we connect the action to its proper slot in the makeConnexions function:
```cpp
connect(find, &QAction::triggered, this, &SpreadSheet::find_function);
```

Here is the implementation of find() function:

```cpp
void MainWindow::find(){

    //Creating the dialog
     Find D;

     //Executing the dialog and storing the user response
     auto reply = D.exec();

     //Checking if the dialog is accepted
     if(reply == Find::Accepted)
     {

         //Getting the search text
         auto search = D.getsearch();
         for(int row=pendingList->count()-1;row>=0;row--){
             if(pendingList->item(row)->text().contains(search)){
                     pendingList->setCurrentRow(row);
//                     pendingList->item(row)->(Qt::);
             }
         }
         for(int row=completedList->count()-1;row>=0;row--){
             if(completedList->item(row)->text().contains(search)){
                     completedList->setCurrentRow(row);
             }
         }
         for(int row=todayList->count()-1;row>=0;row--){
             if(todayList->item(row)->text().contains(search)){
                     todayList->setCurrentRow(row);
             }
         }
     }
}

```
Here is a simple test:

![image](https://user-images.githubusercontent.com/75392302/147773982-3cf804a8-58bd-4ed1-8ab5-f39199d4d410.png)
![image](https://user-images.githubusercontent.com/75392302/147774641-15f220c2-c962-46cb-9750-389582c94461.png)


### Login Dialog

This dialog prompts the user to enter the username and password to go to the main window of the applicaton. 
 
 First we Create a Form Class,
 
 Using the designer obtain the following the form:
 
![image](https://user-images.githubusercontent.com/75392302/147772913-673afc7d-836f-4490-87ac-d52c4916c08e.png)

And we add a private function to verify if the username and passord are correct:
```cpp
void Login::on_pushButton_clicked()
{
    QString username = ui->username->text();
        QString password = ui->password->text();

        if(username ==  "wafae" && password == "hamdaoui"){
            acceptLogin=true;

            QMessageBox::information(this, "Login", "Welcom Wafae !!!         ");
        }else{
            acceptLogin=false;
              QMessageBox::warning(this , "Exit", " Username or password is not correct ");
   }
}
```
**Note**: for this part I prefer to have  a list of users and their passwords . I don't use database concept because I'm not familiar with it , but I will try to use it in the next projects.

**Now we are setup to create the interesting connexion between the login page and mainwondow page**
so we go to the main and we verify if the value of acceptLogin is true , if yes we show mainwindow if not we show a warning  message;
```cpp
int main(int argc, char *argv[])
{
    QApplication a(argc, argv);
    Login loginDialog;
    loginDialog.exec();
    if(loginDialog.acceptLogin){
    MainWindow w;
    w.show();
    return a.exec();}
  return 0;
}
```
**Tests:**
Accept Login:

![image](https://user-images.githubusercontent.com/75392302/147774248-19a068f2-5710-4daa-905b-f0c2e2361086.png)
![image](https://user-images.githubusercontent.com/75392302/147774280-13122ae2-a7fc-4b8b-9a94-9161ddf18e04.png)

![image](https://user-images.githubusercontent.com/75392302/147774396-c203762d-4f5b-4cf2-8c76-e4ae4d4ce15f.png)

Reject Login:

![image](https://user-images.githubusercontent.com/75392302/147774507-93dec6ff-b374-45b5-b882-0f5b9036bfaf.png)
![image](https://user-images.githubusercontent.com/75392302/147774533-6913425d-2a07-4e1f-b7e7-459f9688b93f.png)





Thank you.
