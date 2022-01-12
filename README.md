# Todo-App-with-MVC-version-final

**Notes:**

Please To login use wafae as a username , and 123456 as a password .

## Introduction:
Todo list is an application for organization of a list of tasks you need to complete, or things that you want to do in order of Date (and may be in order of priority). The first part of the project will focus on the core features and skeleton of the application . In part two we’ll take care of text-formatting ( like add, remove, save, clear... ). And in part three, we’ll add some useful extensions like login and find dialog.

**This is what it’ll look like at the end of this project**

![image](https://user-images.githubusercontent.com/75392302/149039858-a4e272de-b883-4657-a647-c954fc2444ab.png)

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
![image](https://user-images.githubusercontent.com/75392302/149039955-19c4db0f-379f-4ca7-8a5e-0932a1292f1f.png)
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
        ("QListWidget { font-size: 9pt; font-weight: bold; }");

        completedList->setStyleSheet
        ("QListWidget { font-size: 9pt; font-weight: bold; }");

        todayList->setStyleSheet
        ("QListWidget { font-size: 9pt; font-weight: bold; }");
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

**Save :** Action to save Tasks in a csv file .
```cpp
   saveActionP = new QAction("Pending List",this);
   saveActionP->setIcon(QIcon(":/pending.jfif"));
   saveActionT = new QAction("Today List",this);
   saveActionT->setIcon(QIcon(":/today.png"));
   saveActionF = new QAction("Finished List",this);
   saveActionF->setIcon(QIcon(":/completed.jfif"));
   connect(saveActionP, &QAction::triggered, this, &MainWindow::savePending);
   connect(saveActionT, &QAction::triggered, this, &MainWindow::saveToday);
   connect(saveActionF, &QAction::triggered, this, &MainWindow::saveFinished);

```
we create a menu and we add those actions to it , using addAction() functions. And we add this menu to Options Menu using addMenu() function.

```cpp
        auto save = OptionsMenu->addMenu("&Save");
        save->addAction(saveActionP);
        save->addAction(saveActionT);
        save->addAction(saveActionF);
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
 
 Using the designer obtain the following form:

![image](https://user-images.githubusercontent.com/75392302/147767524-a1c49f7d-a727-4ddf-87c6-7005ee429b0a.png)

And we add  public Getters and setters for the line edit text  ,  date edit value ,combo box text and finally we need a function verify if the checkBox is Checked or not to get the information about the task and in which list it will be added.
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
void Tasks::setDescription(QString description) const{
    ui->lineEdit->setText(description);
}

void Tasks::setTag(QString tag) const
{
    ui->comboBox->setCurrentText(tag);
}


void Tasks::SetDate(QDate date) const
{
     ui->dateEdit->setDate(date);
}


```
So in add() function we just create the dialog , Execute the dialog and store the user response. Then we check if dialog is accepted , if yes we call Addquery() and loadList() functions. add the task in completed list, if not we check if the date is today date if yes we add task to Today list if not we add it to Pending list.

```cpp

    //Creating the dialog
        Tasks D;

//        Executing the dialog and storing the user response
        auto reply = D.exec();

//        Checking if the dialog is accepted
        if(reply == Tasks::Accepted)
        {
            Addquery(D.getDescription() , D.getTag()  , D.getDate(), D.isCompleted());
            loadList();

        }
```
**Addquery**

So we create a QSqlQuery and we execute the insert query to add a row in sql table , to get values we call tasks getters class. If  we can execute the query we show a critical message .

```cpp
void MainWindow::Addquery(QString description ,QString Tag , QString date, bool finished )
{
    auto query = new QSqlQuery(db);
    QString insert{"INSERT INTO Tasks Values('%1','%2','%3','%4')"};
    if(!query->exec(insert.arg(description).arg(Tag).arg(date).arg(finished)))
         QMessageBox::critical(this,"Error","could not insert the task");
}
```

**LoadList**
for this method we do some test to organise tasks in Tables View .

So we select all row for finished equal true and we add them to complited list ,

And we select all row for date equal to today date and we add them to Today list ,

the rest rows we add them to pending list.

```cpp
void MainWindow::loadList(){
    //model
    auto model = new QSqlQueryModel;
    auto query = new QSqlQuery(db);
    QString view{"SELECT * FROM Tasks WHERE finished=TRUE"};
    query->exec(view);
    model->setQuery(*query);
    completedList->setModel(model);

    auto TodayDate=QDate::currentDate().toString();

    auto model1 = new QSqlQueryModel;
    auto query1 = new QSqlQuery(db);
    QString view1{"SELECT * FROM Tasks WHERE finished=FALSE AND Date='"+TodayDate+"'"};
    query1->exec(view1);
    model1->setQuery(*query1);
    todayList->setModel(model1);


    //pending list:
    auto model2 = new QSqlQueryModel;
    auto query2 = new QSqlQuery(db);
    QString view2{"SELECT * FROM Tasks WHERE finished=FALSE AND Date!='"+TodayDate+"'"};
    query2->exec(view2);
    model2->setQuery(*query2);
//    int rowCount = model2->rowCount();
    pendingList->setModel(model2);


}
```

**Tests:**

![image](https://user-images.githubusercontent.com/75392302/149041929-a529b79d-7c8a-4951-a59d-e5d499abe444.png)

![image](https://user-images.githubusercontent.com/75392302/149041865-693a823f-4722-4830-862f-fa01a7fa15e6.png)
![image](https://user-images.githubusercontent.com/75392302/149042020-b0aa9941-b025-444e-93f1-247e10ea5eac.png)
![image](https://user-images.githubusercontent.com/75392302/149042112-c572823d-0141-4fa1-ac43-cad82b5bdc1f.png)

Here is the result:

![image](https://user-images.githubusercontent.com/75392302/149042155-dc35fc71-d09c-4b2d-9fb2-4f7c57b2791a.png)

***RemoveTask***

When the user wants to delete a Task, we call the private function remove().

when we execute remove() function we got a dialog.

This dialog prompts the user to enter the taks wanted to delete.

 First we Create a Form Class,
 
 Using the designer obtain the following the form:
 
 ![image](https://user-images.githubusercontent.com/75392302/149043064-c4e110a5-ba4c-4ef8-937c-ae5bf8397815.png)

And we add a public Getter for the line edit Text to get the task to remove .

```cpp
QString Remove::getRemoved() const{
    return ui->lineEdit->text();
}
```
So in remove() function we just create the dialog , Execute the dialog and store the user response. Then we check wich list contains this task and remove it using DELETE sql function .
```cpp
void MainWindow::RemoveTask()
{
    auto reply=QMessageBox::question(this,"Remove Task","do you really want to remove this task ??");
     if(reply == QMessageBox::Yes){
         //Creating the dialog
           Remove D;

          //Executing the dialog and storing the user response
           auto reply = D.exec();

           //Checking if the dialog is accepted

          if(reply == Remove::Accepted){
              auto model = new QSqlQueryModel;
              auto query = new QSqlQuery(db);
              QString view{"DELETE FROM Tasks WHERE Description='"+D.getDescription()+"' "};
              query->exec(view);
              model->setQuery(*query);
              pendingList->setModel(model);
              completedList->setModel(model);
              todayList->setModel(model);
              loadList();
          }

     }
}
```
**Test:**

![image](https://user-images.githubusercontent.com/75392302/149043356-ff86510e-cf79-47de-8c9d-7897d1d19883.png)
![image](https://user-images.githubusercontent.com/75392302/149043188-ab1d4dcd-8413-40fe-bdc6-4122e8c4612e.png)
![image](https://user-images.githubusercontent.com/75392302/149043404-44b0ba58-3773-4b64-b49f-b0b138074eb4.png)

Here is the result:

![image](https://user-images.githubusercontent.com/75392302/149043456-14520588-d2f5-43b1-ad6c-5bb8bf046eb7.png)


 ***clear*** 
 
 We just call SQL DELETE function.
 ```cpp
void MainWindow::clear()
{
    //model
    auto model = new QSqlQueryModel;
    auto query = new QSqlQuery(db);
    QString view{"DELETE FROM Tasks "};
    query->exec(view);
    model->setQuery(*query);
    pendingList->setModel(model);
    loadList();
}
 ```
 **Test:**
 
 ![image](https://user-images.githubusercontent.com/75392302/149043777-042000b6-0691-47bd-9c6b-eaf443b03a72.png)
 
 ![image](https://user-images.githubusercontent.com/75392302/149043824-edb0c466-cdca-41f0-94ce-9d119bd5ab39.png)

 
 ***Save***
 
 To save a table view we can go to option menu click on savemenu and choose the list that we want to save.
 ```cpp
 void MainWindow save()
    {
    //Creating a file dialog to choose a file graphically
        auto dialog = new QFileDialog(this);

        //Check if the current file has a name or not
        if(currentFile == "")        {
           currentFile = dialog->getSaveFileName(this,"choose your file");

           //Update the window title with the file name
           setWindowTitle(currentFile);
        }

       //If we have a name simply save the content
       if( currentFile != ""){

               saveList(pendingList,currentFile);
    }
 ```
 Creating a file dialog to choose a file graphically.
 
 Check if the current file has a name or not.
 
 If we have a name simply save the content using saveList() function.
 
 If the user clicks Cancel, the returned file name is empty, and we do nothing.
 
 **saveList()** function store the contenent of a QListWidget in a csv file
 ```cpp
 void MainWindow::saveList(QTableView *listWidget , QString filename){
    QString textData;
    int rows = listWidget->model()->rowCount();
    int columns = listWidget->model()->columnCount();

    // .csv
    QFile csvFile(filename);
    if(csvFile.open(QIODevice::WriteOnly)) {

        QTextStream stream(&csvFile);
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < columns; j++) {

                    textData += listWidget->model()->data(listWidget->model()->index(i,j)).toString();
                    textData += ", "  ;    // for .csv file format
            }
            textData += "\n";
        }
        stream << textData;

        csvFile.close();}
    }
 ```
 **Test:**
 
![image](https://user-images.githubusercontent.com/75392302/149044719-6c887754-930c-4d3c-95c4-a70bdb5bd40f.png)
![image](https://user-images.githubusercontent.com/75392302/149098659-9271f0af-a47d-49df-856f-072dfdb0a783.png)
![image](https://user-images.githubusercontent.com/75392302/149044847-4357e826-ff25-4d0a-b2bd-5abeff4b849b.png)
 
 ***Close***

When the user attempts to close the window, we call the private function Close().
```cpp
   void MainWindow::close()
{
        auto reply = QMessageBox::question(this, "Exit",
                                           "Do you really want to quit?");
        if(reply == QMessageBox::Yes)
            qApp->exit();
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

![image](https://user-images.githubusercontent.com/75392302/149045169-bfa05768-82d4-4145-9778-b772f2a8c48f.png)

And we add a public Getter for the line edit Text to get the cell address:
```cpp
QString Find::find() const
     {
         return ui->lineEdit->text();
     }
```

**Now we are setup to create the interesting connexion between the find_function slot and find actions :**

First we will create the proper slot fpnd() to respond to the action trigger.

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
 //Creating the dialog
        Find D;

       //Executing the dialog and storing the user response
        auto reply = D.exec();

       //Checking if the dialog is accepted
        if(reply == Find::Accepted)
         {

           //Getting the search text
           auto search = D.getsearch();
           for(int row=pendingList->model()->rowCount();row>=0;row--){
               if(pendingList->model()->data(pendingList->model()->index(row,0)).toString()==search)
                   pendingList->setCurrentIndex(pendingList->model()->index(row,0));
           }
           for(int row=todayList->model()->rowCount();row>=0;row--){
               if(todayList->model()->data(todayList->model()->index(row,0)).toString()==search)
                   todayList->setCurrentIndex(todayList->model()->index(row,0));
           }
           for(int row=completedList->model()->rowCount();row>=0;row--){
               if(completedList->model()->data(completedList->model()->index(row,0)).toString()==search)
                   completedList->setCurrentIndex(completedList->model()->index(row,0));
           }


   }


```
Here is a simple test:

![image](https://user-images.githubusercontent.com/75392302/149045627-9ab4f07e-81a4-4761-a60e-18862d0d9c49.png)
![image](https://user-images.githubusercontent.com/75392302/149045955-c0768404-2975-41dd-8af3-feee315853f2.png)

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
