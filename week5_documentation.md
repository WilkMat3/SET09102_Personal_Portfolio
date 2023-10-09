# Documentation

This section is related to your work on clean code and documentation in week 5.

First, choose six rules of clean code and explain them. For each one,

* Summarise the rule in your own words.
* Provide an example from the code that you wrote in week 2 and then refined in week 4.
* Explain how your code implements the rule. 

Second, copy the doxygen comments from your code into your portfolio and provide some 
descriptive commentary on their purpose and structure. Use screenshots showing the HTML 
content that is generated from your code to illustrate your explanation.

Finally, highlight three examples from your code where you have eliminated the need
for comments by adhering to the principles of clean code.
 

## Naming Conventions


### Overview
Naming in programming is extremely important. Understandably, descriptive and relevant names allow us to understand what a particular variable, method or class represents. Martin(2010) in his book writes that the code should read like a poem. There are a few principles regarding naming that help improve how the code reads and reduce the need for comments. While certain rules might differ between languages, several principles are universal to all languages. Below I have listed a few important naming conventions:

--A name should reveal an intention - this helps us to understand the code much better as it can give an idea of what a particular code represents 

--Class names should consist of nouns or noun phrases - a class is a representation of an object, not an action

--Method names should consist of verbs or verb phrases - methods and functions represent some kind of action that is being performed, thus verb is a better option for describing a method

Moreover, C# has its naming conventions. For example, the usage of Pascal Case for classes and methods names or camelCase for local variables, method arguments and private fields names. 

### Code before 
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
        private Organisation EditingOrganisation;

        public OrganisationEditPage(Organisation organisation)
        {
            InitializeComponent();
            EditingOrganisation = organisation;
            NameEntry.Text = organisation.Name;
        }

       private async void SaveButton_Clicked(object sender, EventArgs e)
    {


        string Name1 = NameEntry.Text?.Trim(); // Trim leading/trailing whitespace
        if (string.IsNullOrEmpty(Name1))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; // Do not save if the input name is empty name is empty
        }
        else if (!string.Equals(Name1, EditingOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            // Check if an organization with the same name already exists in the database
            var org = await App.Database.GetOrganisationAsync(Name1);
            if (org != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; // Do not save if a duplicate organization name is found
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditingOrganisation);
                // Update the properties of the EditingOrganisation
                EditingOrganisation.Name = Name1;
                await App.Database.SaveOrganisationAsync(EditingOrganisation); // Update in the database
                                                                               // Navigate back to the OrganisationPage after saving
                await Navigation.PopAsync();
            }
        

        }





    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
        {
            // Navigate back to the OrganisationPage without saving
            await Navigation.PopAsync();
        }
}

```
*Figure 1: Edit Page - code fromw week 3* 

### Code after

```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {


        string inputName = NameEntry.Text?.Trim(); // Trim leading/trailing whitespace
        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; // Do not save if the input name is empty name is empty
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            // Check if an organization with the same name already exists in the database
            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);
            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; // Do not save if a duplicate organization name is found
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);
                // Update the properties of the EditingOrganisation
                EditedOrganisation.Name = inputName;
                await App.Database.SaveOrganisationAsync(EditedOrganisation); // Update in the database
                                                                               // Navigate back to the OrganisationPage after saving
                await Navigation.PopAsync();
            }


        }





    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        // Navigate back to the OrganisationPage without saving
        await Navigation.PopAsync();
    }
}
```
*Figure 2: Edit Page - code from week 5 after naming refactor* 

![Figure 3](./images/NamingChanges.PNG)

*Figure 3: Edit Page - screenshot from week 5 after naming refactor* 

### Explanation

Considering just the naming conventions for the code in Figure 1, I can see that three variables are violating naming conventions. On the [Figure 3] I have highlighted the changes I made. 
Starting from the top following changes were made:

*EditingOrganisation* to *EditedOrganisation* - Editing part of the name was not making much sense and changing to Edited makes it more readable.

the *Name1*  was changed to *inputName* - the initial naming was too general and not written in camelCase as all local variables should be.

Lastly, I have changed the *org* to *organisationWithinDatabase* to better describe what exactly this variable represents 


## Formatting - Vertical formatting rules
### Overview
Vertical formatting rules are designed to make code easier to understand and read. Vertical formatting considers white space between lines. More than one white line is considered a violation of this rule. 
One line gap is permissible, and it is encouraged in order to separate concepts. 
Overall, well-spaced code is more readable. 

### Example
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {


        string inputName = NameEntry.Text?.Trim(); // Trim leading/trailing whitespace
        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; // Do not save if the input name is empty name is empty
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            // Check if an organization with the same name already exists in the database
            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);
            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; // Do not save if a duplicate organization name is found
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);
                // Update the properties of the EditingOrganisation
                EditedOrganisation.Name = inputName;
                await App.Database.SaveOrganisationAsync(EditedOrganisation); // Update in the database
                                                                               // Navigate back to the OrganisationPage after saving
                await Navigation.PopAsync();
            }


        }





    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        // Navigate back to the OrganisationPage without saving
        await Navigation.PopAsync();
    }
}
```
*Figure 4: Edit Page - code before vertical spacing refactor* 

### Code after
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        string inputName = NameEntry.Text?.Trim(); // Trim leading/trailing whitespace

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; // Do not save if the input name is empty name is empty
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            // Check if an organization with the same name already exists in the database
            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);

            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; // Do not save if a duplicate organization name is found
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);
                // Update the properties of the EditingOrganisation
                EditedOrganisation.Name = inputName;

                await App.Database.SaveOrganisationAsync(EditedOrganisation); // Update in the database
                                                                               // Navigate back to the OrganisationPage after saving
                await Navigation.PopAsync();
            }
        }
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        // Navigate back to the OrganisationPage without saving
        await Navigation.PopAsync();
    }
}
```
*Figure 5: Edit Page - code from week 5 after vertical spacing refactor* 

![Figure 6](./images/SpacingChanges.PNG)

*Figure 6: Edit Page - screenshot from week 5 after spacing refactor* 

### Explanation

Initially, I removed all of the white lines. This allows for easier track of encapsulation and makes the class smaller, thus making it more redable

I have also separated some logic with a single empty line as per highlighted sections on the [Figure 6]. This allowed me to keep related logic together and separate lines that perform different roles. 


## Comments
### Overview
Microsoft provides a guide regarding how comments should be formatted and styled. If a comment is required then it should be started with *//*. A space should be made after the initial *//* and a comment should be placed on a separate line. Aside from that they should start with a capital letter and end with a dot. 

Martin(2010) writes that comments do not make up bad code and that it is advisable to create code that is clear enough to explain itself. This can be done through selecting appropriate names for variables and by organising the code.
On the other hand there could be valid reasons to include comments in the code. Such as amplyfing a message about a line of code that seems not too important or a warning agains a code that takes a long time to run.

### Example
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        string inputName = NameEntry.Text?.Trim(); // Trim leading/trailing whitespace

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; // Do not save if the input name is empty name is empty
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            // Check if an organization with the same name already exists in the database
            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);

            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; // Do not save if a duplicate organization name is found
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);
                // Update the properties of the EditingOrganisation
                EditedOrganisation.Name = inputName;

                await App.Database.SaveOrganisationAsync(EditedOrganisation); // Update in the database
                                                                               // Navigate back to the OrganisationPage after saving
                await Navigation.PopAsync();
            }
        }
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        // Navigate back to the OrganisationPage without saving
        await Navigation.PopAsync();
    }
}
```
*Figure 7: Edit Page - code before comments changes* 

### Code after
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim();

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return;
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {

            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);

            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; 
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);

                EditedOrganisation.Name = inputName;

                await App.Database.SaveOrganisationAsync(EditedOrganisation);

                await Navigation.PopAsync();
            }
        }
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        await Navigation.PopAsync();
    }
}
```
*Figure 8: Edit Page - code from week 5 after comments changes* 

![Figure 9](./images/CommentsChanges.PNG)


### Explanation
I have decided to remove all comments but one. As highlighted on the [Figure 9], I have moved a comment to a separate line and changed it, so it amplifies why this line is so important. 
The rest of the comments were removed as they do not provide any value. Changes to the naming of variables and removing the whitespace made this class more readable than before and obvious comments are not required. 



## KISS
### Overview
KISS or Keep It Simple Stupid refers to the importance of code simplicity. The code should be easy to understand for it to be easier to maintain, debug and expand. More complicated code can take significantly longer to debug which increases the cost of the project/task.
### Example
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
    private Organisation EditedOrganisation;

    public OrganisationEditPage(Organisation organisation)
    {
        InitializeComponent();
        EditedOrganisation = organisation;
        NameEntry.Text = organisation.Name;
    }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim();

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return;
        }
        else if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {

            var organisationWithinDatabase = await App.Database.GetOrganisationAsync(inputName);

            if (organisationWithinDatabase != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; 
            }
            else
            {
                await App.Database.DeleteOrganisationAsync(EditedOrganisation);

                EditedOrganisation.Name = inputName;

                await App.Database.SaveOrganisationAsync(EditedOrganisation);

                await Navigation.PopAsync();
            }
        }
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        await Navigation.PopAsync();
    }
}
```
*Figure 10: Edit Page - before KISS refactor* 


### Code after
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
        private Organisation EditedOrganisation;

        public OrganisationEditPage(Organisation organisation)
        {
            InitializeComponent();
            EditedOrganisation = organisation;
            NameEntry.Text = organisation.Name;
        }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim(); 

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; 
        }

        if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            var existingOrganization = await App.Database.GetOrganisationAsync(inputName);
            if (existingOrganization != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; 
            }
        }

        await App.Database.DeleteOrganisationAsync(EditedOrganisation);

        EditedOrganisation.Name = inputName;

        await App.Database.SaveOrganisationAsync(EditedOrganisation);

        await Navigation.PopAsync();
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
        {
            await Navigation.PopAsync();
        }
}

```
*Figure 11: Edit Page - after KISS refactor* 

![Figure 12](./images/KISS.PNG)

### Explanation
Despite the code not being overly complicated, I highlighted the if else blocks inside the *SaveButton_Clicked* method that could be further simplified. Nesting if statements should be avoided if not required and adding extra if else or else coditions increases the complexity.
To combat the complexity I have refactored the method to not use else or else if blocks as per the [Figure 12]. 

## DRY
### Overview
The DRY principle (Don't Repeat Yourself) refers to code reusability. It guides the developer to avoid duplicating code by calling the same functions several times. Instead, it promotes the idea of having a single source for each piece of logic within the code, thus making the codebase easier to update and maintain. 
### Example
```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
        private Organisation EditedOrganisation;

        public OrganisationEditPage(Organisation organisation)
        {
            InitializeComponent();
            EditedOrganisation = organisation;
            NameEntry.Text = organisation.Name;
        }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim(); 

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; 
        }

        if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            var existingOrganization = await App.Database.GetOrganisationAsync(inputName);
            if (existingOrganization != null)
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; 
            }
        }

        await App.Database.DeleteOrganisationAsync(EditedOrganisation);

        EditedOrganisation.Name = inputName;

        await App.Database.SaveOrganisationAsync(EditedOrganisation);

        await Navigation.PopAsync();
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
        {
            await Navigation.PopAsync();
        }
}
```
*Figure 12: Edit Page - Before DRY refactor* 


```
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationAddPage : ContentPage
{
	public OrganisationAddPage()
	{
		InitializeComponent();
	}
    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string organizationName = NameEntry.Text?.Trim(); 

        if (string.IsNullOrEmpty(organizationName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; 
        }

        var existingOrganization = await App.Database.GetOrganisationAsync(organizationName);

        if (existingOrganization != null)
        {
            await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
            return; 
        }

        Organisation newOrganisation = new Organisation
        {
            Name = organizationName,
        };

        await App.Database.SaveOrganisationAsync(newOrganisation); 
        await Navigation.PopAsync();
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        await Navigation.PopAsync();
    }
}
```
*Figure 13: Add Page - Before DRY refactor*

![Figure 14](./images/DRYBefore.PNG)
### Code after
```
namespace Undac.Helpers
{
    public static class InputValidator
    {
        public static async Task<bool> OrganisationExistsInDatabaseAsync(string organisationName)
        {
            var existingOrganization = await App.Database.GetOrganisationAsync(organisationName);

            return existingOrganization != null;
        }
    }
}
```

*Figure 15: New class InputValidator - after DRY refactor* 


```
using Undac.Helpers;
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationAddPage : ContentPage
{
	public OrganisationAddPage()
	{
		InitializeComponent();
	}
    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim(); 

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; 
        }

        if (await InputValidator.OrganisationExistsInDatabaseAsync(inputName))
        {
            await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
            return;
        }

        Organisation newOrganisation = new Organisation
        {
            Name = inputName,
        };

        await App.Database.SaveOrganisationAsync(newOrganisation); 
        await Navigation.PopAsync();
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
    {
        await Navigation.PopAsync();
    }
}
```

*Figure 16: OrganisationAddPage Class - after DRY refactor* 


```
using Undac.Helpers;
using Undac.Models;

namespace Undac.Views.Admin;

public partial class OrganisationEditPage : ContentPage
{
        private Organisation EditedOrganisation;

        public OrganisationEditPage(Organisation organisation)
        {
            InitializeComponent();
            EditedOrganisation = organisation;
            NameEntry.Text = organisation.Name;
        }

    private async void SaveButton_Clicked(object sender, EventArgs e)
    {
        // This trim is importatnt as it prevents ilogical entries to the database. 
        string inputName = NameEntry.Text?.Trim(); 

        if (string.IsNullOrEmpty(inputName))
        {
            await DisplayAlert("Error", "Organization name cannot be empty.", "OK");
            return; 
        }

        if (!string.Equals(inputName, EditedOrganisation.Name, StringComparison.OrdinalIgnoreCase))
        {
            if (await InputValidator.OrganisationExistsInDatabaseAsync(inputName))
            {
                await DisplayAlert("Error", "An organization with the same name already exists.", "OK");
                return; 
            }
        }

        await App.Database.DeleteOrganisationAsync(EditedOrganisation);

        EditedOrganisation.Name = inputName;

        await App.Database.SaveOrganisationAsync(EditedOrganisation);

        await Navigation.PopAsync();
    }

    private async void CancelButton_Clicked(object sender, EventArgs e)
        {
            await Navigation.PopAsync();
        }
}

```

*Figure 17: OrganisationEditPage Class - after DRY refactor* 


### Explanation
My OrganisationEditPage and OrganisationAddPage classes are reusing the same code. On the [Figure 14] I have highlighted areas of the code that violate DRY principle. 

Starting with the top highlight of the [Figure 14]. Creating a method that would trim the input would not make sense, I could potentially put it in validator methods but that would  mean that I have to keep repeating the same line again. This would result in the same amount of repetitions.

Looking at the yellow highlight of the [Figure 14], I decided not create a method that checks if a string is null or empty because I still have to kepp the if block to create an alert and return before saving the input. Aside from that this code is readable and encapsulating that logic would not improve the readablity too much.

The pink highlight on the [Figure 14] was refactored. I created a helper class with input validation method that would encapsulate the check in the database and I reused it across both classes. 


## YAGNI
### Overview
You Ain't Gonna Need It (YAGNI) refers to adding extra functionality to the code. This might include attempts to add a piece of code that could be useful in the future or fulfiling requirements that are not a part of the acceptance criteria. 

### Example
```
using SQLite;
using Undac.Models;

namespace Undac.Data
{
    public interface IRepository<T>
    {
        Task<int> InsertAsync(T entity);
        Task<int> UpdateAsync(T entity);
        Task<int> DeleteAsync(T entity);
    }
    public class Repository<T> : IRepository<T> where T : class
    {
        string DatabasePath;
        public SQLiteAsyncConnection Database;

        public Repository(string databasePath)
        {
            DatabasePath = databasePath;
        }

        public async Task Init()
        {

            if (Database is not null)
                return;

            Database = new SQLiteAsyncConnection(DatabasePath, Constants.Flags);
            var result = await Database.CreateTableAsync<Organisation>();
        }

        public async Task<int> InsertAsync(T entity)
        {
            await Init();
            var result = await Database.InsertAsync(entity);
            return result;
        }

        public async Task<int> UpdateAsync(T entity)
        {
            await Init();
            var result = await Database.UpdateAsync(entity);
            return result;
        }

        public async Task<int> DeleteAsync(T entity)
        {
            await Init();
            var result = await Database.DeleteAsync(entity);
            return result;
        }
    }
}
```
*Figure 18: Generic class for database access* 

### Explanation

While working on my ticket I decided to create a class that would possibly simplify the process of saving items to the database and reduce code needed to implement saving various models to the database.

Unfortunately, this is wrong for few reasons. Firstly, this is not a part of my ticket and I have been wasting time that was meant to be spent on acceptance criteria for my ticket. 
We have not decided as a team how we are going to approach this issues and there is a disscussion to be had about the implementation. We could potentially find a better solution than my approach.
As a result I have deleted the class. 





## Doxygen Comments




## Eliminating comments

### 1
### 2
### 3

## References
Alls, J. (2020). Clean Code in C#. Packt Publishing Ltd.

Martin, R. C. (2010). Clean code a handbook of agile software craftmanship. Upper Saddle River [Etc.] Prentice Hall.
‌
Microsoft. (2023, August 1). C# identifier names. Retrieved October 2, 2023, from learn.microsoft.com website: https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/identifier-names

Microsoft (Ed.). (2023b, June 15). C# Coding Conventions. Retrieved October 5, 2023, from learn.microsoft.com website: https://learn.microsoft.com/en-us/dotnet/csharp/fundamentals/coding-style/coding-conventions
‌
‌

[Figure 3]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/NamingChanges.PNG "Figure 3"
[Figure 6]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/NamingChanges.PNG "Figure 6"
[Figure 9]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/CommentsChanges.PNG "Figure 9"
[Figure 12]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/Kiss.PNG "Figure 12"
[Figure 14]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/DRYBefore.PNG "Figure 14"
[Figure 6]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/NamingChanges.PNG "Figure 6"
[Figure 6]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/NamingChanges.PNG "Figure 6"