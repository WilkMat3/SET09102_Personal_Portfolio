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

The *Login* method can access the *User* object along with the salt parameter, but this implementation does not encapsulate the data and leaves the details of the object exposed. The code snippet does not provide the rest of the class but the calls for *CreatingSalt()* and *HashPassword()* are made from within the class contaning the Login and Register methods which could be seen as violating the SRP as those are separate concerns. 
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
```
*Challenge 4 Authentication Solution - User Class* 


```
    public static class Encryptor
    {
        public static string HashPassword(string password, string salt)
        {
            // implementation for hashing password
        }
        public static string CreateSalt()
        {
            // implementation for creating salt
        }
    }

    public class Program
    {
        public void Register(string username, string password)
        {
            SaveToDatabase(new User(username, password));
        }

        public bool IsPasswordValid(string username, string password)
        {
            var user = GetUserFromDatabase(username);

            return user.ValidateInputPassword(password);
        }
    }
```

*Challenge 4 Authentication Solution - Program & Encryptor classes* 

### Register mehthod changes
To prevent extra parameters from being passed, I have encapsulated the data passed in the *SaveToDatabase* method into a *User* object. I have also removed password hashing from both the *Login* and *Register* methods. The *User* object data now is exposing methods not data, thus following best practice on encapsulation (Alls,2020). I followed it up by assigning *salt* and hashing the password within the constructor of the *User* object, as those details do not need to be exposed to the Program class.

### Login method changes
I have changed the *Login* method name to better describe what it actually does. Martin(2010) writes about selecting intention-revealing names, and the *IsPasswordValid* name is better at describing what the method tries to accomplish.
I have extracted a method from the previous *Login* method to reduce access to the data within the *User* object. The *AssignPassword* method within the *User* class handels the check for validity of the password without the need to expose hashed password to the Program class. 
To abide by the DRY principle, I removed two *return* statements from the *Login* methods and refactored them into one line, to improve the readbility of the code. 

### Other Changes
For the purpose of this exercise I have named the class contaning *Register* and *IsPasswordValid*. The *Program* name was created for the simplicity, as the initial snippet did not provide the name of the actual class.
I have given the *HashPassword* and *CreateSalt* their own class as those methods seem to be more related to encrypting password rather than preforming login or register actions. This removed the concern for encrypring action from the *Program* class. 

## References
Alls, J. (2020). Clean Code in C#. Packt Publishing Ltd.

Martin, R. C. (2010). Clean code a handbook of agile software craftmanship. Upper Saddle River [Etc.] Prentice Hall.
‌