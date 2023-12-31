# Setup

## Environment configuration

This section describes my development environment set up for the Purple Group project.

### GitHub Shared Repository

![image 1](./images/Repository_My_access_Github.PNG)
*Image 1*

On the [image 1][image1], I have highlighted in red the name of the repository (top left) and my name (bottom right) in the Contributors tab, therefore confirming that I have access to the project's repository. Setting up the actual repository was not a difficult task. Any GitHub desktop user can click the "+" on the top right of the screen, which would open up options. After clicking on "create repository," a user needs to name the repository and optionally change other settings. After clicking "Create repository," the process is completed. In the case of our project, we have set the repository to private - we did not want anyone else to copy our work. Aside from that, the person responsible for creating the repository had to invite other users, which is also a trivial process. This option shows up on the main screen of the private GitHub repository, and after selecting it, a user is required to provide GitHub handles.

### GitHub Project Task Board

![image 2](./images/github_project_task_board.PNG)
*Image 2*

To track tasks and assign work tickets, we created a Project Task Board using GitHub Projects. On the [image 2][image2], the project name and my name were highlighted to confirm my access. Creating a Task Board was as simple as clicking "Projects" when on the Group Project repository website and linking a new project to the repository.

### Visual Studio Configuration

![image 3](./images/vs_cloned.PNG)
*Image 3*

On the [image 3][image3], I have highlighted the push and pull links for the group project, as well as an avatar with my initials, therefore confirming that my Visual Studio was linked with our GitHub repository. In my case, the only required configuration was to clone the repository in Visual Studio. I am using VS 2022, and I had relevant MAUI packages installed.

![image 4](./images/MAUI.PNG)
*Image 4*

To correctly set up Visual Studio 2022 for MAUI app development, users have to select the MAUI package during installation, as highlighted in the [image 4][image4].

To clone the repository, users have to obtain the link for the repository from its GitHub page. This can be done by clicking the green "clone" button and copying the repository link, as highlighted on the [image 5][image5].

![image 5](./images/LinkingRepository.PNG)
*Image 5*

The next step requires opening Visual Studio, clicking File in the top right corner, and then clicking "Clone repository." Provided that the user is authenticated with GitHub, after pasting the link the user can clone the repository. The [image 6][image6] shows the screen for cloning the repository.

![image 6](./images/VS_repository.PNG)
*Image 6*

## Reflection

### Alternative Configurations

While our current environment is well-suited to our project, there are other options in terms of the choice of IDE, version control, and even the project board. Visual Studio 2022 is a great choice when developing MAUI apps because of its support by Microsoft. The JetBrains Rider IDE also supports MAUI app development. We could also change GitHub to Azure DevOps for version control; both allow for creating workflows and are quite similar. When it comes to the project board, GitHub Projects is the best choice, but there are other alternatives like Jira or Trello. Github projects allow us to link tasks to pull requests and add extra descriptions and labels to each ticket. It does that without the need to pay extra. Moreover, the vast majority of agile project management platforms limit the number of contributors on the free tier, which makes them unusable in our case.

### Challenges

While the process of setting up the development environment was trivial, we have discovered that some people in our group are not used to GitHub or have no experience using Visual Studio. To help each other, we have set up a communication channel on Discord where we can share our issues and ideas. This will be especially helpful in the future when we are dealing with more complicated tasks.

---

[image1]: ./images/Repository_My_access_Github.PNG "Image 1"
[image2]: ./images/github_project_task_board.PNG "Image 2"
[image3]: ./images/vs_cloned.PNG "Image 3"
[image4]: ./images/MAUI.PNG "Image 4"
[image5]: ./images/LinkingRepository.PNG "Image 5"
[image6]: ./images/VS_repository.PNG "Image 6"
