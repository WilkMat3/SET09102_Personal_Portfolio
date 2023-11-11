# Project work 2

## Summary
This week I have been building on the work from last week by working on the [Maintain view lists of partner agencies (UN, International, national, NGO, voluntary)](https://github.com/xinjoonha/SET09102_PURPLE/issues/64). Previously I have struggled to implement the MVVM pattern for the ViewAllOrganisationsPage but this week I have been able to incorporate it into my code. I have resorted to using MVVM community toolkit version 8.0.0 as it was the latest one that have not caused any issues when running the application. In summary I made following changes to the code:

*+ added view model for ViewAllorganisationsPage


*+ changed xaml file so it can work with the view model


*+ added lazy initialization to the repository pattern 


*+ registered singletons for the view, view model, repositories.


*+ cleaned up not needed database initialization within the App.xamls.cs


```
<?xml version="1.0" encoding="utf-8" ?>
<ContentPage xmlns="http://schemas.microsoft.com/dotnet/2021/maui"
             xmlns:x="http://schemas.microsoft.com/winfx/2009/xaml"
             x:Class="Undac.Views.ViewLists.ViewAllOrganisationsPage"
             xmlns:viewmodel ="clr-namespace:Undac.ViewModels"
             xmlns:model="clr-namespace:Undac.Models"
             x:DataType="viewmodel:ViewAllOrganisationsViewModel"
             BackgroundColor="{DynamicResource PageBackgroundColor}"
             Padding="16"
             Title="Search Organisations">
    <StackLayout>
        <Label Text="Filter Organisations" FontSize="Large" TextColor="{DynamicResource PrimaryTextColor}" HorizontalOptions="Center" Margin="10,20,10,0" />

        <SearchBar x:Name="FullTextSearchBar" SearchCommand ="{Binding FilterOrganisationsCommand}" Placeholder="Search by full text" Margin="10,10,10,0" BackgroundColor="{DynamicResource SearchBarBackgroundColor}" Text="{Binding FullNameFilter}" />
        <SearchBar x:Name="CurrentAssociationSearchBar" SearchCommand ="{Binding FilterOrganisationsCommand}"  Placeholder="Search by Current Association Status" Margin="10,10,10,0" BackgroundColor="{DynamicResource SearchBarBackgroundColor}" Text="{Binding AssociationFilter}" />

        <ListView x:Name="OrganisationListView" ItemsSource="{Binding FilteredOrganisations}" ItemTapped="OnOrganisationSelected" SeparatorColor="{DynamicResource ListViewSeparatorColor}">
            <ListView.ItemTemplate>
                <DataTemplate x:DataType="model:Organisation">
                    <ViewCell>
                        <StackLayout Margin="0,10,0,10" Padding="10" BackgroundColor="{DynamicResource ListItemBackgroundColor}">
                            <Label Text="{Binding Name}" FontAttributes="Bold" FontSize="16" TextColor="{DynamicResource TitleTextColor}" />
                            <Label Text="{Binding CurrentAssociation}" FontAttributes="Bold" FontSize="12" TextColor="Yellow" />
                        </StackLayout>
                    </ViewCell>
                </DataTemplate>
            </ListView.ItemTemplate>
        </ListView>
    </StackLayout>
</ContentPage>
```

*Figure 1: Xaml for the ViewAllOrganisationPage*

I made several changes to the xaml file for the ViewModel to work with the UI.
I had to add references to the *ViewAllOrganisationViewModel* but also to the *Undac.Models* as can be seen in the top part of the Figure 1. I specified the viewmodel namespace and the view model itself for this xaml file.
The addition of UndacModel reference is because of the need to display a list of organisation objects. 


```
   <SearchBar x:Name="FullTextSearchBar" SearchCommand ="{Binding FilterOrganisationsCommand}" Placeholder="Search by full text" Margin="10,10,10,0" BackgroundColor="{DynamicResource SearchBarBackgroundColor}" Text="{Binding FullNameFilter}" />
   <SearchBar x:Name="CurrentAssociationSearchBar" SearchCommand ="{Binding FilterOrganisationsCommand}"  Placeholder="Search by Current Association Status" Margin="10,10,10,0" BackgroundColor="{DynamicResource SearchBarBackgroundColor}" Text="
```

*Figure 2: Xaml - Search bars*


```
        [RelayCommand]
        public void FilterOrganisations()
        {
            FilteredOrganisations = new ObservableCollection<Organisation>(AllOrganisation);
            var filteredOrganisations = FilteredOrganisations.ToList();
           
            if (!string.IsNullOrWhiteSpace(fullNameFilter))
            {
                filteredOrganisations = FilterMethods.FilterFullText(filteredOrganisations, fullNameFilter.ToLower());
            }

            if (!string.IsNullOrWhiteSpace(associationFilter))
            {
                filteredOrganisations = FilterMethods.FilterAssociation(filteredOrganisations, associationFilter.ToLower());
            }

            FilteredOrganisations = new ObservableCollection<Organisation>(filteredOrganisations);
        }
```

*Figure 3: ModelView - Search bar logic*

I have added Search commands to the xaml which I bound with the method/command in the view model using the RelayCommand annotiation as can be seen in Figure 2 and 3. The logic for filtering is moved to a different static class that handles the filtering.
I had to keep two separte lists for organisations to make sure that filtering runs smoothly. 


```
        public string FullNameFilter
        {
            get => FullNameFilter;
            set
            {
                SetProperty(ref fullNameFilter, value);
                FilterOrganisationsCommand.Execute(null);
            }
        }

        public string AssociationFilter
        {
            get => associationFilter;
            set
            {
                SetProperty(ref associationFilter, value);
                FilterOrganisationsCommand.Execute(null);
            }
        }
```
*Figure 4: ModelView - Triggering the command*

As per the Figure 4, anytime one of the search bars changes the FilterOrganisation command/method is executed. This is somehting I came across thanks to the ChatGpt when trying to figure out how to trigger the filter method for search bars.


```
        public ViewAllOrganisationsViewModel(IOrganisationRepository organisationRepository)
        {
            _organisationRepository = organisationRepository;
            LoadOrganisations();
        }
```

*Figure 5: ModelView - Constructor*


```
        builder.Services.AddSingleton<IOrganisationRepository, OrganisationRepository>();
        builder.Services.AddSingleton<ViewAllOrganisationsPage>();
        builder.Services.AddTransient<OrganisationStatusPage>();
```

*Figure 6: Dependecy Injection - registering singletons for dependecy injection service*

In the constructor for the view model(Fig 5), I inject the interface required for loading organisations from the database the LoadOrganisation methods just retrieves list of organisations from the database. I also registered both with MAUI dependency injection service(Fig 6). 


```
public partial class ViewAllOrganisationsPage : ContentPage
{
    private IOrganisationRepository _organisationRepository;

    private ViewAllOrganisationsViewModel _viewModel;

    public ViewAllOrganisationsPage( ViewAllOrganisationsViewModel viewModel, IOrganisationRepository repository)
	{
        _organisationRepository = repository;
        BindingContext = viewModel;
        InitializeComponent();
    }

    protected override async void OnAppearing()
    {
        base.OnAppearing();
        if (_viewModel == null)
        {
            _viewModel = new ViewAllOrganisationsViewModel(_organisationRepository); 
            BindingContext = _viewModel; 
        }
        _viewModel.LoadOrganisations(); 
    }

    private async void OnOrganisationSelected(object sender, ItemTappedEventArgs e)
    {
        if (e.Item is Organisation organisation)
        {
            await Navigation.PushAsync(new OrganisationStatusPage(organisation, _organisationRepository));
        }
    }
}
```
*Figure 7: View - changes to the view code behind class*

Thanks to the view model my View class shrank significantly. I only required the OnAppearing method because of the Page needing to navigate to the a page where we change the status of the organisation and on return it needs to update the view. 
Aside fronm that I am handling an event where user navigates to another page to edit status of an organisation. 
Last week I did not implement this functionality because of few reason but one of then was connected to initialising component before binding context was set.

```

    public class Repository<TEntity> : IRepository<TEntity> where TEntity : class, new()
    {

        public readonly UndacDatabase database;

        public Repository(UndacDatabase database)
        {
            this.database = database;

        }

        public async Task<List<TEntity>> GetAllAsync()
        {
            await database.Init();
            return await database.Connection.Table<TEntity>().ToListAsync();
        }

        public async Task<TEntity> GetAsync(int id)
        {
            await database.Init();
            var query = database.Connection.Table<TEntity>();
            var idProperty = typeof(TEntity).GetProperty("Id");

            if (idProperty != null && idProperty.PropertyType == typeof(int))
            {
                return await database.Connection.FindAsync<TEntity>(id);
            }
            else
            {
                throw new InvalidOperationException($"The entity of type {typeof(TEntity).Name} does not have an 'Id' property.");
            }

            return await query.FirstOrDefaultAsync();
        }

        public async Task<int> SaveAsync(TEntity item)
        {
            await database.Init();
            if (item == null)
                throw new ArgumentNullException(nameof(item));

            var identifiable = item as IIdentifiable;
            if (identifiable != null && identifiable.Id != 0)
            {
                return await database.Connection.UpdateAsync(item);
            }
            else
            {
                return await database.Connection.InsertAsync(item);
            }
        }

        public async Task<int> DeleteAsync(TEntity item)
        {
            await database.Init();
            if (item == null)
                throw new ArgumentNullException(nameof(item));

            return await database.Connection.DeleteAsync(item);
        }
    }
```

*Figure 8: Repository - changes to the class*

Another important part of my update is that I have used lazy initialisation. Firstly instead of passing the SQLlite connection in the constructor I inject the database. This allowed me to add database.Init() to each method thus allowing for lazy initialisation when a method is called.


## Testing

I have conducted extensive manual testing and ensured all of the unit tests pass. This PR is an improvement to the work from previous week thus my tests remained the same. I have extracted the methods for filtering the organisations list to a static class. Below I are my tests:

```
        [Test]
        public void FilterFullNameTest()
        {
            var testOrganisation = new Organisation
            {
                Name = "Test",
                CurrentAssociation = "Pending"
            };
            var testOrganisation2 = new Organisation
            {
                Name = "No Common",
                CurrentAssociation = "Approved"
            };

            List<Organisation> organisationList = new List<Organisation>();
            organisationList.Add(testOrganisation);
            organisationList.Add(testOrganisation2);

            List<Organisation> filteredList = FilterMethods.FilterFullText(organisationList, "Test");

            var filteredName = filteredList.First().Name;
            Assert.Multiple(() =>
            {
                Assert.That(filteredList.Count, Is.EqualTo(1), "List is too long");
                Assert.That(filteredList, Is.Not.Null, "filtered list should not be null");
                Assert.That(filteredName, Is.EqualTo(testOrganisation.Name), "Filter was not applied");
            });

        }
```

*Figure 9: Testing FullFilter*

In Figure 9 I have checked how text is filtered for the full filter. I provided two objects to check that only filtered result is returned.


```
        [Test]
        public void FilterAssociationTest()
        {
            var testOrganisation = new Organisation
            {
                Name = "Test",
                CurrentAssociation = "Pending"
            };
            var testOrganisation2 = new Organisation
            {
                Name = "No Common",
                CurrentAssociation = "Approved"
            };

            List<Organisation> organisationList = new List<Organisation>();
            organisationList.Add(testOrganisation);
            organisationList.Add(testOrganisation2);

            List<Organisation> filteredList = FilterMethods.FilterAssociation(organisationList, testOrganisation2.CurrentAssociation);

            var filteredName = filteredList.First().CurrentAssociation;

            Assert.Multiple(() =>
            {
                Assert.That(filteredList.Count, Is.EqualTo(1), "List is too long");
                Assert.That(filteredList, Is.Not.Null, "filtered list should not be null");
                Assert.That(filteredName, Is.EqualTo(testOrganisation2.CurrentAssociation), "Filter was not applied");
            });
        }
```

*Figure 10: Testing AssociationFilter*

Similar to the other test method I have created one that tests association status as per Figure 10.


```
        [Test]
        public void FilterFullNameReturnMultipleTestsTest()
        {
            var testOrganisation = new Organisation
            {
                Name = "Test",
                CurrentAssociation = "Pending"
            };
            var testOrganisation2 = new Organisation
            {
                Name = "No Common",
                CurrentAssociation = "Approved"
            };
            var testOrganisation3 = new Organisation
            {
                Name = "Test213",
                CurrentAssociation = "Pending"
            };

            List<Organisation> organisationList = new List<Organisation>();
            organisationList.Add(testOrganisation);
            organisationList.Add(testOrganisation2);
            organisationList.Add(testOrganisation3);

            List<Organisation> filteredList = FilterMethods.FilterFullText(organisationList, testOrganisation.Name);

            var filteredName = filteredList.First().Name;
            Assert.Multiple(() =>
            {
                Assert.That(filteredList.Count, Is.EqualTo(2), "List length is not correct");
                Assert.That(filteredList, Is.Not.Null, "filtered list should not be null");
                Assert.That(filteredName, Is.EqualTo(testOrganisation.Name), "Filter was not applied");
            });

        }
```

*Figure 11: Testing returning multiple results on the Full Filter*


```
        [Test]
        public void FilterAssociationReturnMultipleTest()
        {
            var testOrganisation = new Organisation
            {
                Name = "Test",
                CurrentAssociation = "Pending"
            };
            var testOrganisation2 = new Organisation
            {
                Name = "No Common",
                CurrentAssociation = "Approved"
            };
            var testOrganisation3 = new Organisation
            {
                Name = "No Common2",
                CurrentAssociation = "Approved"
            };

            List<Organisation> organisationList = new List<Organisation>();
            organisationList.Add(testOrganisation);
            organisationList.Add(testOrganisation2);
            organisationList.Add(testOrganisation3);

            List<Organisation> filteredList = FilterMethods.FilterAssociation(organisationList, testOrganisation2.CurrentAssociation);

            var filteredName = filteredList.First().CurrentAssociation;

            Assert.Multiple(() =>
            {
                Assert.That(filteredList.Count, Is.EqualTo(2), "List length is not correct");
                Assert.That(filteredList, Is.Not.Null, "filtered list should not be null");
                Assert.That(filteredName, Is.EqualTo(testOrganisation2.CurrentAssociation), "Filter was not applied");
            });
        }
```
*Figure 12: Testing returning multiple results on the Association Filter*

I have also wanted to test how filtering works when there are partial matches for more than one result as per Figures 11 and 12.


```
        [Test]
        public void FilterFullNameReturnNothingTestsTest()
        {
            var testOrganisation = new Organisation
            {
                Name = "Test",
                CurrentAssociation = "Pending"
            };
            var testOrganisation2 = new Organisation
            {
                Name = "No Common",
                CurrentAssociation = "Approved"
            };
            var testOrganisation3 = new Organisation
            {
                Name = "Test213",
                CurrentAssociation = "Pending"
            };

            List<Organisation> organisationList = new List<Organisation>();
            organisationList.Add(testOrganisation);
            organisationList.Add(testOrganisation2);
            organisationList.Add(testOrganisation3);

            List<Organisation> filteredList = FilterMethods.FilterFullText(organisationList, "Abracadabra");

            Assert.That(filteredList.Count, Is.EqualTo(0), "List length is not correct");
        }
```
*Figure 13: Testing no matching result*

Finally, I have considered the case when there is no match for the searched phrase and I have tested if the method will result in returning an empty list. 

## Getting my code reviewed
I am not getting a large amount of code reviews despite asking multiple times on the discord group. I know that there are few reasons for it. Firstly, we do not have too many engaged group memebers. Secondly, I try to innovate every week and add something new and valuable to the project. This means that people are not familiar with the new functionality(Repository pattern or MVVM) and they do not have much to add apart from review from perspective of coding conventions and formatting. 
This week I received a review from my colleague in which he pointed out few mistakes that I repeated in the past. I struggle with empty lines and forget about removing unused references. I am dislexic so I have to take extra care when reviewing formatting of my code but there seem to be always something that I miss. I think I should start looking into some kind of linting tool to help me with that. 

This week I only had one occurence of an extra empty line in my code:

![Figure 14](./images/Comment_emptyLine.PNG)

*Figure 14: Review Code - Empty line 

To address this I removed the empty line, I think linting tool would make sense since I keep repeating my mistakes. On the positive side I only had one occurence of this issue in this PR.




## Leading a code review


## Reflections

### Teamwork

### Challanges & learnings



### My personal workflow

Recently, I have been struggling with staying focused when working on project for multiple hours. I have multiple courseworks and assesment and every day I study multiple hours without having much time to relax. As I am not a robot sometimes my mind wanders and I cannot focus on what I wanted to do. On top of that there are many distractions around and sometimes I cannot focus on the task at hand.
I decided to try a pomodoro approach after a conversation with one of my colleagues from the group. For every 50 minutes of work I take a 10 minute break. Additionally, I removed all other distractions from my room(my phone).

This allowed me to complete more work within few hours than what I used to complete in a day. By separating time for work and rest I became more efficient and organised.
Retrospectively, I regret not doing it before but overall I have shown that I can adapt when things do not go as planned and I can find a solution to a problem.
This might seem not related to the subject but in the real world we do have to complete projects on time and being efficient is a necessity when working with Agile methodology. 


[Figure 14]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/Comment_emptyLine.png "Figure 14"

[Figure 15]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/KISS_review.png "Figure 15"

[Figure 16]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/Constructor_review.png "Figure 16"

[Figure 17]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/KISS_review.png "Figure 17"

[Figure 18]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/Constructor_review.png "Figure 18"

[Figure 19]: https://github.com/WilkMat3/SET09102_Personal_Portfolio/blob/main/images/KISS_review.png "Figure 19"

Week 10 is the third and last week in a series in which the goal is to improve your 
personal software engineering practice. Your portfolio entry has the same general content
as last week's, including:

* A descriptive summary of the issue that you worked on.
* Snippets from your code with commentary showing how you have used good software design 
  practice.
* A descriptive summary of the test code that you have written.
* A reflective summary of any changes that were requested during the code review along 
  with your fixes.
* A descriptive summary of any issues you found with the code that you were asked to review.
* A general reflective section that identifies, for example,
  * New things you have realised this week
  * Common problems that can arise in a team development situation
  * How your practice compares to other people's
  * etc.

Be sure to include links to the original items in the team's GitHub repository.

In the reflective sections this week, you should highlight ways that you persona practice
has improved as before. It would also be good to reflect on any improvements that have
been made to the agreed team workflow and related procedures. Are things working
better than they were? What further improvements could be made in the future?
