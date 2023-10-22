# Testing

The practical exercise in week 6 involved competitive testing. For your portfolio entry, select two pieces of test code that you wrote that best illustrate your skills in this area. If you were working with someone else - for example, while using pair programming - Make sure that is clear in your discussion.

For each example

    Summarise the purpose of the code you were testing
    Include the test code
    Provide a brief explanation of the test(s) that are performed
    Explain why this is an important aspect of the code to test
    Identify any limitations of your tests (this may be something that you realised after the evaluation).

Did you manage to write a test which failed during the final evaluation? If so, that would make an excellent example. You should briefly discuss why the writer of the code might have overlooked the particular test case that failed.

## Overview

In this practical exercise I have created tests and features implementation along with my colleague using pair programming. 

## Example one

### Summary 
This code is testing the logic within *OnAttemptSubmitted* method. Specifically tests are checking how the remaining attempts are affected by valid and invalid input. 

### Test Code 
```
namespace HangmanTests
{
    public class OnAttemptSubmittedTests
    {
        Mock<IUserInputProvider> mockInputProvider;
        public OnAttemptSubmittedTests()
        {
            mockInputProvider = new Mock<IUserInputProvider>();
        }

        [Theory]
        [InlineData('a', 7)] 
        [InlineData('b', 6)]
        public void OnAttemptSubmitted_DecrementsRemainingAttempts(char expectedInput, int expectedRemainingAttempts)
        {

            mockInputProvider.Setup(x => x.GetUserInput()).Returns(expectedInput);

            var gamePage = new GamePage("Easy");
            gamePage.userInputProvider = mockInputProvider.Object;

            gamePage.Word = "apple";

            gamePage.OnAttemptSubmitted(this, new EventArgs());

            Assert.Equal(expectedRemainingAttempts, gamePage.remainingAttempts); 

        }
    }
}
```
*Figure 1: Testing the OnAttemptSubmitted() method*

### Testing Strategy
I have arranged the code according to the Arrange, Act and Assert rule. I have created an mock input provider class to mock the UI. Specifically, I needed to do this to mock the input from the user. I have also arranged for a specific word to ensure that tests are consistent. This concluded the arrange part of the test.

The Act part of the test is done by the *gamePage.OnAttemptSubmitted(this, new EventArgs());* call. I have added *IUserInputProvider* to the project along with a *UserInputProvider* class that is used when the application is running normally. IUserInputProvider is simply responsible for retrieving the data from the user input. 
This is a unit test and not a platform specific test, UI does not render so I swapped the UI for a mock object to simulate data provided by users.

The Assert part compares the expected remaining attemts with actual attempts. I used the Theory and InlineData syntax to provide test cases for invalid and valid input. This way I did not have to write the same test again but using different data. 

### Reason for testing
This part of the functionality is crucial for the game logic. We want to know that the user is not penalised for guessing correctly but also we want to make sure that a chance is subtracted every time a user guesses incorrectly.
I was trying to use moq to preform testing and I found that mocking the  UserInputProvider (which would ideally preform all the validation on the input) would mean that we lose some points in the competition as this was not part of the original code. On the other hand doing so enhanced my learning as I had to learn more about dependcy injections and mocking. 

### Limitations
This code tests two states, user could guess the letter or not. But it dos not check invalid inputs. Apart from that I am mocking the UI in this test, specifically the Entry field from UI which provides the input for this tests. On some level I am not testing the actual implementation as I am not using UI. There is an argumant here about testing other cases as validation could be done at a different level and this method does not have to handle invalid input. 

## Example two

### Summary 
This code is testing the logic within *CreateNewChallenge* method. Specifically those tests are checking if each game uses valid word for the difficulty level. 

### Test Code 
```
namespace HangmanTests.MateuszW_Tests
{
    public class CreateNewChallengeTests
    {
        [Fact]
        public void EasyGameIsCreated()
        {
            GamePage game = new GamePage("Easy");

            game.CreateNewChallenge();
            Assert.True(game.Word.Length < 7);
        }

        [Fact]
        public void MediumGameIsCreated()
        {
            GamePage game = new GamePage("Medium");

            game.CreateNewChallenge();
            Assert.True(game.Word.Length >= 7 & game.Word.Length < 10);
        }

        [Fact]
        public void HardGameIsCreated()
        {
            GamePage game = new GamePage("Hard");

            game.CreateNewChallenge();
            Assert.True(game.Word.Length >= 10);
        }

    }
}

```
*Figure 2: Testing the OnAttemptSubmitted() method*

### Testing Strategy
Simillarly to the other test example, tests are arragned in the Arrange, Act and Assert rule. A new game is initiated with a specific level. 

The act part is the call *CreateNewChallenge* method which is higier order method. As a result of calls within that method a suitable word is selected from a list of strings. 

The assert part of those tests is checking the length of each word as this determines the difficulty level.

### Reason for testing
Testing if that method produces a word with desired length allows us to determine if difficulty level is implemented correctly. 

### Limitations
There is another part of this method that is to reset the display but a unit test should test only one functionality and checking the display can be done separately. The input is not tested for other options. We only handle 3 different strings. Everything else is treated as a random difficulty game. There is no option for the user to modify the string as this is passed from clicking one of three buttons with a difficulty level written on a label. The input is not modifiable in another way.  
