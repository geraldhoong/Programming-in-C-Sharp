
# Module 8: Accessing Remote Data

## Lab: Retrieving and Modifying Grade Data Remotely

### Lab Setup

Estimated Time: **60 minutes**

### Preparation Steps

1. Ensure that you have cloned the 20483C directory from GitHub. It contains the code segments for this course's labs and demos. (**https://github.com/MicrosoftLearning/20483-Programming-in-C-Sharp/tree/master/Allfiles**)
2. Initialize the database:
   - In the **Apps** list, click **File Explorer**.
   - In File Explorer, navigate to the **[Repository Root]\Allfiles\Mod08\Labfiles\Databases** folder, and then double-click **SetupSchoolGradesDB.cmd**.
        > **Note:** If a Windows protected your PC dialog appears, click **More info** and then click **Run Anyway**. Close File Explorer.
3. Ensure that you have completed the following steps before starting work on this module:
   - Install **Microsoft.OData.ConnectedService.vsix** from **[Repository Root]\AllFiles\Assets**.
   - Install **WcfDataServices.exe** from **[Repository Root]\AllFiles\Assets**.
   - Run **WCF.reg** file from **[Repository Root]\AllFiles\Assets**.

### Exercise 1: Creating a WCF Data Service for the SchoolGrades Database

#### Task 1: Configure data service in the Grades.Web project

1. Open **Visual Studio 2017**.
2. In **Visual Studio**, on the **File** menu, point to **Open**, and then click **Project/Solution**.
3. In the **Open Project** dialog box, browse to **[Repository Root]\Allfiles\Mod08\Labfiles\Starter\Exercise 1**, click **GradesPrototype.sln**, and then click **Open**.
    > **Note:** If any Security warning dialog box appears, clear **Ask me for every project in this solution** check box and then click **OK**.
4. In **Solution Explorer**, right-click the **GradesPrototype** solution, and then click **Restore NuGet Packages**.
5. In the **Grades.Web** project, right-click on **Referencs**, and then click **Add Reference**.
6. In the **Reference Manager – Grades.Web** dialog box do the following steps:
   - Click **Projects**,and then select **Grades.DataModel**.
   - Click **Browse**.
   - In the **Select the files to reference** dialog box, browse to the **[Repository Root]\Allfiles\Mod08\Labfiles\Starter\Exercise 1\packages\EntityFramework.5.0.0\lib\net45** folder, click **EntityFramework.dll**, and click **Add**.
   - Click **OK**.
7. In Solution Explorer, expand the **GradesPrototype** project, and then double-click **App.config**.
8. In the code editor, copy the following XML to the clipboard:
    ```xml
    <connectionStrings>
        <add name="SchoolGradesDBEntities" connectionString="metadata=res://*/GradesModel.csdl|res://*/GradesModel.ssdl|res://*/GradesModel.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=(localdb)\MSSQLLocalDB;initial catalog=SchoolGradesDB;integrated security=True;MultipleActiveResultSets=True;App=EntityFramework&quot;" providerName="System.Data.EntityClient" />
    </connectionStrings>
    ```
9. In **Solution Explorer**, in the **Grades.Web** project, double-click **Web.config**.
10. Click at the end of the opening **\<configuration\>** element, press Enter, and then paste the contents of the clipboard.

#### Task 2: Specify the GradesDBEntities data context for the data service

1. In **Solution Explorer**, in **Grades.Web** project, expand **Services**, and then double-click **GradesWebDataService.svc**.
2. In the code editor, type the following code under all the **using**:
    ```cs
    using Grades.DataModel;
    ```
3. On the **View** menu, click **Task List**.
4. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2a: Replace the object keyword with your data source class name** task.
5. In the code editor, locate the comment **TODO: Exercise 1: Task 2a: Replace the object keyword with your data source class name**, and then type the following code instead of the **object** keyword:
    ```cs
    SchoolGradesDBEntities
    ```
6. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2b: set rules to indicate which entity sets and service operations are visible, updatable, etc** task.
7. In the code editor, click at the end of the comment line, press Enter, and then type the following code:
    ```cs
    // Configure all entity sets to permit read and write access.
    config.SetEntitySetAccessRule("Grades", EntitySetRights.All);
    config.SetEntitySetAccessRule("Teachers", EntitySetRights.All);
    config.SetEntitySetAccessRule("Students", EntitySetRights.All);
    config.SetEntitySetAccessRule("Subjects", EntitySetRights.All);
    config.SetEntitySetAccessRule("Users", EntitySetRights.All);
    ```

#### Task 3: Add an operation to retrieve all of the students in a specified class

1. Click after the closing braces for the **InitializeService** method, press Enter twice, and then type the following code:
    ```cs
    // Find all students in a specified class.
    [WebGet]
    public IEnumerable<Student> StudentsInClass(string className)
    {
        var students = from Student s in this.CurrentDataSource.Students
                       where String.Equals(s.Teacher.Class, className)
                       select s;

        return students;
    }
    ```
2. In the **Task List** window, double-click the **TODO: Exercise 1: Task 2b: set rules to indicate which entity sets and service operations are visible, updatable, etc**. task.
3. In the code editor, click at the end of the comment line, press Enter, and then type the following code:
    ```cs
    // Configure the StudentsInClass operation as read-only.
    config.SetServiceOperationAccessRule("StudentsInClass", ServiceOperationRights.AllRead);
    ```

#### Task 4: Build and test the data service

1. On the **Build** menu, click **Build Solution**.
2. In **Solution Explore**r, in the **Grades.Web** project, in the **Services** folder, right-click **GradesWebDataService.svc**, and then click **View in Browser (Microsoft Edge)**.
3. In Microsoft Edge, if the **Intranet settings are turned off by default** message appears, click **Don't show this message again**.
4. Verify that Microsoft Edge displays an XML description of the entities that Data Service exposes.
5. Close the Browser.
6. In Visual Studio, on the **File** menu, click **Close Solution**.

>**Result:** After completing this exercise, you should have configured a WCF Data Service in the application to provide remote access to the **SchoolGrades** database.

### Exercise 2: Integrating the Data Service into the Application

#### Task 1: Add an OData Connected Service for the WCF Data Service to the GradesPrototype application

1. In Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.
2. In the **Open Project** dialog box, browse to **[Repository Root]\Allfiles\Mod08\Labfiles\Starter\Exercise 2**, click **GradesPrototype.sln**, and then click **Open**.
    > **Note:** If any Security warning dialog box appears, clear **Ask me for every project in this solution** check box and then click **OK**.
3. In **Solution Explorer**, right-click the **GradesPrototype** solution, and then click **Set StartUp Projects**.
4. In the **Solution 'GradesPrototype' Property Pages** dialog box, click **Multiple startup projects**.
5. In the **Action** column for **Grades.Web** and **GradesPrototype**, click**Start**, and then click **OK**.
6. On the **Build** menu, click **Rebuild Solution**.
7. In Solution Explorer, in the **Grades.Web** project, in the **Services** folder, right-click **GradesWebDataService.svc**, and then click **View in Browser (Microsoft Edge)**.
8. In **Solution Explorer**, expand **GradesPrototype**, expand **References**, right-click **Grades.DataModel,** and then click **Remove**.
9. Right-click **References**, and then click **Add Connected Service**.
10. In the **Connected Services** window, choose **OData Connected Service**.
11. In the **Configure endpoint** dialog box, in the **Service name** text box, type **Grades.DataModel**.
12. In the **Address** text box, type **http://localhost:1655/Services/GradesWebDataService.svc/$metadata**, and then click **Next**.
13. Click **Finish** and wait until the process completes.
14. In **Solution Explorer**, in the **GradesPrototype** project, expand **Connected Services**, expand the **Grades.DataModel** folder, and then double-click **Reference.cs**.
15. In the code editor, modify the **namespace SchoolGradesDBModel** code to look like the following code:
    ```cs
    namespace Grades.DataModel
    ```
16. In **Solution Explorer**, right-click the **GradesPrototype** project, point to **Add**, and then click **New Folder**.
17. Delete the existing name, type **DataModel**, and then press Enter.
18. In **Solution Explorer**, expand the **Grades.DataModel** project, right-click **Classes.cs**, and then click **Copy**.
19. In **GradesPrototype**, right-click **DataModel**, and then click **Paste**.
20. In **Grades.DataModel**, right-click **customGrade.cs**, and then click **Copy**.
21. In **GradesPrototype**, right-click **DataModel**, and then click **Paste**.
22. In **Grades.DataModel**, right-click **customTeacher.cs**, and then click **Copy**.
23. In **GradesPrototype**, right-click **DataModel**, and then click **Paste**.

#### Task 2: Modify the code that accesses the EDM to use the WCF Data Service

1. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2a: Specify the URL of the GradesWebDataService** task.
2. In the code editor, below the comment, click inside the parentheses, and then type the following code:
    ```cs
    new Uri("http://localhost:1655/Services/GradesWebDataService.svc")
    ```
3. Add the following code to the **SessionContext** class, after the **Save** method:
    ```cs
    static SessionContext()
    {
        DBContext.MergeOption = System.Data.Services.Client.MergeOption.PreserveChanges;
    }
    ```
4. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2b: Load User and Grades data with Students** task.
5. In the code editor, at the end of the comment, press Enter, and then type the following code:
    ```cs
    SessionContext.DBContext.LoadProperty(student, "User");
    SessionContext.DBContext.LoadProperty(student, "Grades");
    ```
6. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2c: Load User and Students data with Teachers** task.
7. In the code editor, below the comment, click at the end of the **SessionContext.DBContext.Teachers** code, and then type the following code:
    ```cs
    .Expand("User, Students")
    ```
8. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2d: Load User and Grades data with Students** task.
9. In the code editor, below the comment, click at the end of the **SessionContext.DBContext.Students** code, and then type the following code:
    ```cs
    .Expand("User, Grades")
    ```
10. In **Solution Explorer**, in the **GradesPrototype** project, expand **DataModel**, and then double-click **customTeacher.cs**.
11. Click at the end of the **using System.Threading.Tasks** code, press Enter, and then type the following code:
    ```cs
    using GradesPrototype.Services;
    ```
12. In the code editor, locate the **TODO: Exercise 2: Task 2e: Refer to the Students collection in the SessionContext.DBContext object** comment in the **customTeacher.cs** file. There are two comments with this text. This is because the comment is located in the **customTeacher.cs** file that you copied from the **Grades.DataModel** project. Make sure that you modify the **customTeacher.cs** file in the **GradesPrototype** project.
13. In the line below the comment, delete the word **Students**, and then type the following code:
    ```cs
    SessionContext.DBContext.Students
    ```
14. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2f: Reference the SessionContext.DBContext.Students collection**.
15. In the code editor, below the comment, change
    ```cs
    SessionContext.DBContext.Students.Local
    ```
    to the following code:
    ```cs
    SessionContext.DBContext.Students.Expand("User, Grades")
    ```
16. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2g: Use the AddToGrades method to add a new grade.**
17. In the code editor, below the comment, change
    ```cs
    SessionContext.DBContext.Grades.Add(newGrade);
    ```
    to the following code:
    ```cs
    SessionContext.DBContext.AddToGrades(newGrade);
    ```
18. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2h: Load Subject data with Grades** task.
19. In the code editor, below the comment, change **SessionContext.DBContext.Grades** to the following code:
    ```cs
    SessionContext.DBContext.Grades.Expand("Subject")
    ```
20. In the **Task List** window, double-click the **TODO: Exercise 2: Task 2i: Use the AddToStudents method to add a new student** task.
21. In the code editor, below the comment, change
    ```cs
    SessionContext.DBContext.Students.Add(newStudent);
    ```
    to the following code:
    ```cs
    SessionContext.DBContext.AddToStudents(newStudent);
    ```

#### Task 3: Modify the code that saves changes back to the database to use the WCF Data Service

1. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3a: Specify that the selected student has been changed** task.
2. In the code editor, click in the blank space below the comment, and then type the following code:
    ```cs
    SessionContext.DBContext.UpdateObject(student);
    ```
3. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3b: Specify that the current student has been changed** task.
4. In the code editor, click in the blank space below the comment, and then type the following code:
    ```cs
    SessionContext.DBContext.UpdateObject(SessionContext.CurrentStudent);
    ```
5. In the **Task List** window, double-click the **TODO: Exercise 2: Task 3c: Specify that the current user has been changed** task.
6. Click in the blank space below the comment, and then type the following code:
    ```cs
    SessionContext.DBContext.UpdateObject(currentUser);
    ```

#### Task 4: Build and test the application to verify that the application still functions correctly

1. On the **Build** menu, click **Build Solution**.
2. On the **Debug** menu, click **Start Without Debugging**.
3. In the **Username** text box, type **vallee**.
4. In the **Password** text box, type **password99**, and then click **Log on**.
5. In the student list, click **Eric Gruber**, and then click **Remove Student**.
6. In the **Confirm** dialog box, click **Yes**.
7. Verify that **Eric Gruber** is removed from the student list.
8. Click **Enroll Student**.
9. In the **Assign Student** dialog box, click **Jon Orton**.
10. In the **Confirm** dialog box, click **Yes**.
11. In the **Assign Student** dialog box, click **Close**, and then verify that **Jon Orton** is added to the student list.
12. Click **Change Password**.
13. In the **Change Password Dialog** dialog box, in the **Old Password** text box, type **password99**.
14. In the **New Password** text box, type **password88**.
15. In the **Confirm** text box, type **password88**, and then click **OK**.
16. In the **Password** dialog box, click **OK**, and then click **Log off**.
17. Click **Log on**, verify that the **Logon Failed** dialog box appears, and then click **OK**.
18. In the **Password** text box, type **password88**, and then click **Log on**.
19. Verify that the student list is displayed.
20. Click **Log off**, and then in the **Username** text box, type **grubere**.
21. In the **Password** text box, type **password**, and then click **Log on**.
22. Verify that the student profile for Eric Gruber appears, and then click **Log off**.
23. Close the application.
24. In Visual Studio, on the **File** menu, click **Close Solution**.

>**Result:** After completing this exercise, you should have updated the **Grades Prototype** application to use OData Connected Service.

### Exercise 3: Retrieving Student Photographs Over the Web (If Time Permits)

#### Task 1: Create the ImageNameConverter value converter class

1. In Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.
2. In the **Open Project** dialog box, browse to **[Repository Root]\Allfiles\Mod08\Labfiles\Starter\Exercise 3**, click **GradesPrototype.sln**, and then click **Open**.
    > **Note:** If any Security warning dialog box appears, clear **Ask me for every project in this solution** check box and then click **OK**.
3. In **Solution Explorer**, right-click the **GradesPrototype** solution, and then click **Set StartUp Projects**.
4. In the **Solution 'GradesPrototype' Property Pages** dialog box, click **Multiple startup projects**.
5. In the **Action** column for **Grades.Web** and **GradesPrototype**, click **Start**, and then click **OK**.
6. On the **Build** menu, click **Rebuild Solution**.
7. In the **Task List** window, double-click the **TODO: Exercise 3: Task 1: Create the ImageNameConverter value converter to convert the image name of a student photograph into the URL of the image on the Web server** task.
8. Click at the end of the **// Converter class for transforming an image name for a photograph into a URL** comment, press Enter, and then type the following code:
    ```cs
    public class ImageNameConverter : IValueConverter
    {
    }
    ```
9. In the **ImageNameConverter** class, type the following code:
    ```cs
    const string webFolder = "http://localhost:1650/Images/Portraits/";
    ```
10. Right-click the **IValueConverter** keyword, point to **Quick Actions and refactorings**, and then click **Implement Interface**.
11. In the **Convert** method, delete the existing statement that throws a **NotImplementedException**, and then type the following code:
    ```cs
    string fileName = value as string;

    if (fileName != null)
    {
        return string.Format("{0}{1}", webFolder, fileName);
    }
    else
    {
        return string.Empty;
    }
    ```
12. On the **Build** menu, click **Build Solution**.

#### Task 2: Add an Image control to the StudentsPage view and bind it to the ImageName property

1. In **Solution Explorer**, in the **GradesPrototype** project, expand **Views**, and then double-click **StudentsPage.xaml**.
2. In the XAML editor, in the **UserControl** element at the top of the markup, click after the **xmlns:d="http://schemas.microsoft.com/expression/blend/2008"** line, press Enter, and then type the following markup:
    ```xml
    xmlns:local="clr-namespace:GradesPrototype.Views"
    ```
3. Locate the **TODO: Exercise 3: Task 2a. Add an instance of the ImageNameConverter class as a resource to the view** comment, click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <UserControl.Resources>
        <local:ImageNameConverter x:Key="ImageNameConverter"/>
    </UserControl.Resources>
    ```
4. Locate the **TODO: Exercise 3: Task 2b. Add an Image control to display the photo of the student** comment, click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <Image Height="100" />
    ```
5. Locate the **Exercise 3: Task 2c. Bind the Image control to the ImageName property and use the ImageNameConverter to convert the image name into a URL** comment.
6. In the line above the comment, modify the
    ```xml
    <Image Height="100" />
    ```
    markup to look like the following markup:
    ```xml
    <Image Height="100" Source="{Binding ImageName, Converter={StaticResource ImageNameConverter}}" />
    ```

#### Task 3: Add an Image control to the StudentProfile view and bind it to the ImageName property

1. In Solution Explorer, double-click **StudentProfile.xaml**.
2. Locate the **TODO: Exercise 3: Task 3a. Add an instance of the ImageNameConverter class as a resource to the view** comment.
3. Click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <app:ImageNameConverter x:Key="ImageNameConverter"/>
    ```
4. Locate the **TODO: Exercise 3: Task 3b. Add an Image control to display the photo of the student and bind the Image control to the ImageName property and use the ImageNameConverter to convert the image name into a URL** comment.
5. Click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <Image Height="150" Source="{Binding ImageName, Converter={StaticResource ImageNameConverter}}" />
    ```

#### Task 4: Add an Image control to the AssignStudentDialog control and bind it to the ImageName property

1. In **Solution Explorer**, expand **Controls**, and then double-click **AssignStudentDialog.xaml**.
2. In the XAML editor, at the top of the markup, click at the end of the **xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"** line, press Enter, and then type the following markup:
    ```xml
    xmlns:local="clr-namespace:GradesPrototype.Views"
    ```
3. Locate the **TODO: Exercise 3: Task 4a. Add an instance of the ImageNameConverter class as a resource to the view** comment.
4. Click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <Window.Resources>
        <local:ImageNameConverter x:Key="ImageNameConverter"/>
    </Window.Resources>
    ```
5. Locate the **TODO: Exercise 3: Task 4b. Add an Image control to display the photo of the student and bind the control to the ImageName property and use the ImageNameConverter to convert the image name into a URL** comment.
6. Click at the end of the comment, press Enter, and then type the following markup:
    ```xml
    <Image Height="100" Source="{Binding ImageName, Converter = {StaticResource ImageNameConverter}}" />
    ```

#### Task 5: Build and test the application, verifying that student’s photographs appear in the list of students for the teacher

1. On the **Build** menu, click **Build Solution**.
2. On the **Debug** menu, click **Start Without Debugging**.
3. In the **Username** text box, type **vallee**.
4. In the **Password** text box, type **password88**, and then click **Log on**.
5. Verify that the student list now includes images.
6. Click **George Li**, and then verify that the student profile appears with an image.
7. Click **Remove Student**.
8. In the **Confirm** dialog box, click **Yes**.
9. Click **Enroll Student**.
10. In the **Assign Student** dialog box, verify that each of the unassigned students has an image.
11. Click **George Li**.
12. In the **Confirm** dialog box, click **Yes**.
13. In the **Assign Student** dialog box, click **Close**.
14. Verify that George Li is added to the student list with an image.
15. Close the application.
16. On the **File** menu, click **Close Solution**.

>**Result:** After completing this exercise, the student list, student profile, and unassigned student dialog box will display the images of students that were retrieved across the web.

©2018 Microsoft Corporation. All rights reserved.

The text in this document is available under the  [Creative Commons Attribution 3.0 License](https://creativecommons.org/licenses/by/3.0/legalcode), additional terms may apply. All other content contained in this document (including, without limitation, trademarks, logos, images, etc.) are  **not**  included within the Creative Commons license grant. This document does not provide you with any legal rights to any intellectual property in any Microsoft product. You may copy and use this document for your internal, reference purposes.

This document is provided &quot;as-is.&quot; Information and views expressed in this document, including URL and other Internet Web site references, may change without notice. You bear the risk of using it. Some examples are for illustration only and are fictitious. No real association is intended or inferred. Microsoft makes no warranties, express or implied, with respect to the information provided here.
