# Module 10: Improving Application Performance and Responsiveness

## Lab: Improving the Responsiveness and Performance of the Application

### Lab Setup

Estimated Time: **75 minutes**

### Preparation Steps

1. Ensure that you have cloned the 20483C directory from GitHub. It contains the code segments for this course's labs and demos. (**https://github.com/MicrosoftLearning/20483-Programming-in-C-Sharp/tree/master/Allfiles**)
2. Initialize database:
   - In the **Apps** list, click **File Explorer**.
   - Navigate to the **[Repository Root]\AllFiles\Mod10\Labfiles\Databases** folder, and then double-click **SetupSchoolGradesDB.cmd**.
        > **Note :** If a Windows protected your PC dialog appears, click More info and then click Run Anyway.
   - Close **File Explorer**.

### Exercise 1: Ensuring That the UI Remains Responsive When Retrieving Teacher Data

#### Task 1: Build and run the application

1. Open **Visual Studio 2017**.
2. On the **File** menu, point to **Open**, and then click **Project/Solution**.
3. In the **Open Project** dialog box, browse to **[Repository Root]\AllFiles\Mod10\Labfiles\Starter\Exercise 1**, click **Grades.sln**, and then click **Open**.
    > **Note:** If any Security warning dialog box appears, clear **Ask me for every project in this solution** check box and then click **OK**.
4. In **Solution Explorer**, right-click **Solution ‘Grades’**, and then click **Properties**.
5. On the **Startup Project** page, click **Multiple startup projects**, set **Grades.Web** and **Grades.WPF** to **Start**, and then click **OK**.
6. On the **Build** menu, click **Build Solution**.
7. On the **Debug** menu, click **Start Without Debugging**.
8. When the application loads, in the **Username** text box, type **vallee**, and in the **Password** text box, type **password99**, and then click **Log on**.
9. Notice that the UI briefly freezes while fetching the list of students for Esther Valle (try moving the application window after logging on but before the list of students appears).
10. Close the application window.

#### Task 2: Modify the code that retrieves teacher data to run asynchronously

1. On the **View** menu, click **Task List**.
2. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2a: Convert GetTeacher into an async method that returns a Task\<Teacher>** task.
3. In the code editor, delete the following line of code:
    ```cs
    public Teacher GetTeacher(string userName)
    ```
4. In the blank line below the comment, type the following code:
    ```cs
    public async Task<Teacher> GetTeacher(string userName)
    ```
5. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2b: Perform the LINQ query to fetch Teacher information asynchronously** task.
6. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    var teacher = await Task.Run(() =>
        (from t in DBContext.Teachers
         where t.User.UserName == userName
         select t).FirstOrDefault());
    ```
7. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2c: Mark MainWindow.Refresh as an asynchronous method** task.
8. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    public async void Refresh()
    ```
9. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2d: Call GetTeacher asynchronously** task.
10. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    var teacher = await utils.GetTeacher(SessionContext.UserName);
    ```

#### Task 3: Modify the code that retrieves and displays the list of students for a teacher to run asynchronously

1. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3a: Mark StudentsPage.Refresh as an asynchronous method** task.
2. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    public async void Refresh()
    ```
3. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3b: Implement the OnGetStudentsByTeacherComplete callback to display the students for a teacher here** task.
4. In the blank line below the comment, type the following code:
    ```cs
    private void OnGetStudentsByTeacherComplete(IEnumerable<Student> students)
    {

    }
    ```
5. In the **Task List** window, double-click the **Exercise 1: Task 3c: Relocate the remaining code in this method to create the OnGetStudentsByTeacherComplete callback (in the Callbacks region)** task.
6. In the code editor, move all of the code between the comment and the end of the **Refresh** method to the **Clipboard**.
7. In the **Task List**  window, double-click the **TODO: Exercise 1: Task 3b: Implement the OnGetStudentsByTeacherComplete callback to display the students for a teacher here** task.
8. Click in the blank line between the curly braces and paste the code from the **Clipboard**.
9. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3d: Use a Dispatcher object to update the UI** task.
10. In the code editor, click at the end of the comment line, press Enter, and then type the following code:
    ```cs
    this.Dispatcher.Invoke(() => {
    ```
11. Immediately after the last line of code in the method, type the following code:
    ```cs
    });
    ```
12. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3e: Convert GetStudentsByTeacher into an async method that invokes a callback** task.
13. In the code editor, delete the following line of code:
    ```cs
    public List<Student> GetStudentsByTeacher(string teacherName)
    ```
14. In the blank line below the comment, type the following code:
    ```cs
    public async Task GetStudentsByTeacher(string teacherName,  Action<IEnumerable<Student>> callback)
    ```
15. In the code editor, modify the return statement below the **if(!IsConnected())** line to return without passing a value to the caller:
    ```cs
    return;
    ```
16. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3f: Perform the LINQ query to fetch Student data asynchronously** task.
17. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    var students = await Task.Run(() =>
        (from s in DBContext.Students
         where s.Teacher.User.UserName == teacherName
         select s).OrderBy(s => s.LastName).ToList());
    ```
18. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3g: Run the callback by using a new task rather than returning a list of students** task.
19. In the code editor, delete the following code:
    ```cs
    return students;
    ```
20. In the blank line below the comment, type the following code:
    ```cs
    await Task.Run(() => callback(students));
    ```
21. In the **Task List** window, double-click the **TODO: Exercise 1: Task 3h: Invoke GetStudentsByTeacher asynchronously and pass the OnGetStudentsByTeacherComplete callback as the second argument** task.
22. In the code editor, modify the statement below the comment as shown in bold below:
    ```cs
    await utils.GetStudentsByTeacher(SessionContext.UserName, OnGetStudentsByTeacherComplete);
    ```

#### Task 4: Build and test the application

1. On the **Build** menu, click **Build Solution**.
2. On the **Debug** menu, click **Start Without Debugging**.
3. When the application loads, in the **Username** text box, type **vallee**, and in the **Password** text box, type **password99**, and then click **Log on**.
4. Verify that the application is more responsive than before while fetching the list of students for **Esther Valle**, and then close the application window.
5. On the **File** menu, click **Close Solution**.

> **Result:** After completing this exercise, you should have updated the **Grades** application to retrieve data asynchronously.

### Exercise 2: Providing Visual Feedback During Long-Running Operations

#### Task 1: Create the BusyIndicator user control

1. In **Visual Studio**, on the **File** menu, point to **Open**, and then click **Project/Solution**.
2. In the **Open Project** dialog box, browse to **[Repository Root]\AllFiles\Mod10\Labfiles\Starter\Exercise 2**, click **Grades.sln**, and then click **Open**.
    > **Note:** If any Security warning dialog box appears, clear **Ask me for every project in this solution** check box and then click **OK**.
3. In **Solution Explorer**, right-click **Solution ‘Grades’**, and then click **Properties**.
4. On the **Startup Project** page, click **Multiple startup projects**, set **Grades.Web** and **Grades.WPF** to **Start**, and then click **OK**.
5. On the **Build** menu, click **Build Solution**.
6. In **Solution Explorer**, right-click **Grades.WPF**, point to **Add**, and then click **User Control**.
7. In the **Name** text box, type **BusyIndicator.xaml**, and then click **Add**.
8. In **Solution Explorer**, expand **Grades.WPF**, and then drag **BusyIndicator.xaml** into the **Controls** folder.
   > **Note:** It is better to create the user control at the project level and then move it into the **Controls** folder when it is created. This ensures that the user control is created in the same namespace as other project resources.
9. In the **BusyIndicator.xaml** file, in the **UserControl** element, delete the following attributes:
    ```xml
    d:DesignWidth="450" d:DesignHeight="800"
    ```
10. Modify the **Grid** element to include a **Background** attribute, as the following markup shows:
    ```xml
    <Grid Background="#99000000">

    </Grid>
    ```
11. Type the following markup between the opening and closing **Grid** tags:
    ```xml
    <Border CornerRadius="6"
            HorizontalAlignment="Center"
            VerticalAlignment="Center">
        <Border.Background>
            <LinearGradientBrush>
                <GradientStop Color="LightGray" Offset="0" />
                <GradientStop Color="DarkGray" Offset="1" />
            </LinearGradientBrush>
        </Border.Background>
        <Border.Effect>
            <DropShadowEffect Opacity="0.75" />
        </Border.Effect>
    </Border>
    ```
12. On the blank line before the closing **Border** tag, type the following code:
    ```xml
    <Grid Margin="10">
        <Grid.RowDefinitions>
            <RowDefinition Height="Auto" />
            <RowDefinition Height="Auto" />
        </Grid.RowDefinitions>

    </Grid>
    ```
13. On the blank line before the closing **Grid** tag, type the following code:
    ```xml
    <ProgressBar x:Name="progress"
                 IsIndeterminate="True"
                 Width="200"
                 Height="25"
                 Margin="20" />
    ```
14. Click after the end of the **ProgressBar** element, and then press Enter.
15. In the new line, type the following code:
    ```xml
    <TextBlock x:Name="txtMessage"
               Grid.Row="1"
               FontSize="14"  
               FontFamily="Verdana"
               Text="Please Wait..."
               TextAlignment="Center" />
    ```
16. On the **File** menu, click **Save All**.
17. In **Solution Explorer**, expand **Grades.WPF**, and then double-click **MainWindow.xaml**.
18. Towards the bottom of the **MainWindow.xaml** file, locate the **TODO: Exercise 2: Task 1b: Add the BusyIndicator control to MainWindow** comment.
19. Click at the end of the comment, press Enter, and then type the following code:
    ```xml
    <y:BusyIndicator x:Name="busyIndicator"
                     Margin="0"
                     Visibility="Collapsed" />
    ```
20. On the **Build** menu, click **Build Solution**.

#### Task 2: Add StartBusy and EndBusy event handler methods

1. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2a: Implement the StartBusy event handler** task.
2. In the blank line below the comment, type the following code:
    ```cs
    private void StartBusy(object sender, EventArgs e)
    {
        busyIndicator.Visibility = Visibility.Visible;
    }
    ```
3. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2b: Implement the EndBusy event handler** task.
4. In the blank line below the comment, type the following code:
    ```cs
    private void EndBusy(object sender, EventArgs e)
    {
        busyIndicator.Visibility = Visibility.Hidden;
    }
    ```

#### Task 3: Raise the StartBusy and EndBusy events

1. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3a: Add the StartBusy public event** task.
2. In the blank line below the comment, type the following code:
    ```cs
    public event EventHandler StartBusy;
    ```
3. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3b: Add the EndBusy public event** task.
4. In the blank line below the comment, type the following code:
    ```cs
    public event EventHandler EndBusy;
    ```
5. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3c: Implement the StartBusyEvent method to raise the StartBusy event** task.
6. In the blank line below the comment, type the following code:
    ```cs
    private void StartBusyEvent()
    {
        if (StartBusy != null)
            StartBusy(this, new EventArgs());
    }
    ```
7. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3d: Implement the EndBusyEvent method to raise the EndBusy event** task.
8. In the blank line below the comment, type the following code:
    ```cs
    private void EndBusyEvent()
    {
        if (EndBusy != null)
            EndBusy(this, new EventArgs());
    }
    ```
9. In **Solution Explorer**, double-click **MainWindow.xaml**.
10. In the **MainWindow.xaml** file, locate the **TODO: Exercise 2: Task 3e: Wire up the StartBusy and EndBusy event handlers for the StudentsPage view** comment.
11. Immediately below the comment, modify the **StudentsPage** element to include **StartBusy** and **EndBusy** attributes, as the following code shows:
    ```xml
    <y:StudentsPage x:Name="studentsPage"
                    StartBusy="StartBusy"
                    EndBusy="EndBusy"
                    StudentSelected="studentsPage_StudentSelected"
                    Visibility="Collapsed" />
    ```
12. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3f: Raise the StartBusy event** task.
13. In the blank line below the comment, type the following code:
    ```cs
    StartBusyEvent();
    ```
14. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3g: Raise the EndBusy event** task.
15. In the blank line below the comment, type the following code:
    ```cs
    EndBusyEvent();
    ```

#### Task 4: Build and test the application

1. On the **Build** menu, click **Build Solution**.
2. On the **Debug** menu, click **Start Without Debugging**.
3. When the application loads, in the **Username** text box, type **vallee**, and in the **Password** text box, type **password99**, and then click **Log on**.
4. Verify that the application displays the busy indicator while waiting for the list of students to load, and then close the application window.
5. On the **File** menu, click **Close Solution**.

> **Result:** After completing this exercise, you should have updated the **Grades** application to display a progress indicator while the application is retrieving data.

©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
