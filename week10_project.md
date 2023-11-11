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
*Figure 7: View - changes to the class*

Thanks to the view model my View class shrank significantly. I only required the OnAppearing method because of the Page needing to navigate to the a page where we change the status of the organisation and on return it needs to update the view. 
Aside fronm that I am handling an event where user navigates to another page to edit status of an organisation. 
Last week I did not implement this functionality because of few reason but one of then was connected to initialising component before binding context was set.



## Testing

## Getting my code reviewed


## Leading a code review


## Reflections

### Teamwork

### Challanges & learnings



### My personal workflow

Recently, I have been struggling with staying focused when working on project for multiple hours. I have multiple courseworks and assesment and every day I study multiple hours without having much time to relax. As I am not a robot sometimes my mind wanders and I cannot focus on what I wanted to do on top of that there are many distractions around and sometimes I cannot focus on the task at hand.
I decided to try a pomodoro approach. For every 50 minutes of work I take a 10 minute break. Additionally, I removed all other distractions from my room(my phone).

This allowed me to complete more work within few hours than what I used to complete in a day. By separating time for work and rest I became more efficient and organised.
Retrospectively, I regret not doing it before but overall I have shown that I can adapt when things do not go as planned and I can find a solution to a problem
This might seem not related to the subject but in the real world we do have to complete projects on time and being efficient is a necessity when working with Agile methodology. 


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
