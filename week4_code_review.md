# Code review
I have chosen challenge number 4 titled Authentication for my code review. This section will discuss commentary about the initial code, provide my solution to the problem and explain my reasoning behind it. 

```
public void Register(string username, string password){ 
  var salt = CreateSalt(); 
  var hashedPassword = HashPassword(password, salt); 
  SaveToDatabase(username, hashedPassword, salt); 
} 

public bool Login(string username, string password){ 
  var user = GetUserFromDatabase(username); 
  var hashedPassword = HashPassword(password, user.Salt); 

  if(user.HashedPassword == hashedPassword){ 
    return true; 
  } 
  return false; 
}
```
*Challenge 4 Authentication* 

## The problem
Looking at the *Register* method we can see that it saves the user to the database but before that, it calls a method to generate a salt and hashes the password with that salt. The call to save the user data to the database has three parameters and according to Alls(2020), methods preferably should have less than 3 parameters. 

The *Login* method can access the *User* object along with the salt parameter, but this implementation does not encapsulate the data and leaves the details of the object exposed.
The *Login* method name is probably not the most suitable as the actual method compares two variables and returns a boolean instead of performing a login action. 
The DRY principle is violated because of the way the return statement is written. 

## The Solution
```
 public class User
    {
        public string Username { get; private set;}
        private string Password { get; set;}
        private string Salt { get; set; }

        public User(string username, string password)
        {
            Username = username;
            AssignSalt();
            AssignPassword(password);
        }

        public bool ValidateInputPassword(string inputPassword)
        {
            return Password == Encryptor.HashPassword(inputPassword, Salt);
        }

        private void AssignSalt()
        {
           Salt = Encryptor.CreateSalt();
        }

        private void AssignPassword(string password)
        {
            Password = Encryptor.HashPassword(password,Salt);
        }
    }
--------------------------------------------------------------------------------------------------------
        // rest of the class omitted for brevity

        public void Register(string username, string password)
        {
            SaveToDatabase(new User(username, password)); 
        }

        public bool IsPasswordValid(string username, string password){

            return GetUserFromDatabase(username).ValidateUserPassword(password); 
        }
```
*Challenge 4 Authentication - Solution* 

To prevent extra parameters from being passed, I have encapsulated the data passed in the *SaveToDatabase* method into a *User* object. I have also removed password hashing from both the *Login* and *Register* methods. The *User* object data now is exposing methods not data, thus following best practice on encapsulation (Alls,2020). I followed it up by assigning *salt* and hashing the password within the constructor of the *User* object, as those details should not be exposed unless required.
I have changed the *Login* method name to better describe what it actually does. Martin(2010) writes about selecting intention-revealing names and the *IsPasswordValid* name is better at describing what the method tries to accomplish.
I have extracted a method from the previous *Login* method to reduce access to the data within the *User* object. 
To abide by the DRY principle, I removed two *return* statements from the *Login* methods and refactored them into one line, to improve the readbility of the code. 

## References
Alls, J. (2020). Writing Clean Functions - Clean Code in C# [Book]. Retrieved September 27, 2023, from www.oreilly.com website: https://learning.oreilly.com/library/view/clean-code-in/9781838982973/87ecc9d9-a240-4058-ac19-4921da271303.xhtml#uuid-0a990568-6e52-49a8-8ad0-748587f1b9bf

Martin, R. C. (2010). Clean code a handbook of agile software craftmanship. Upper Saddle River [Etc.] Prentice Hall.
‌