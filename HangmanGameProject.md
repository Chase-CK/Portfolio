# Hangman Game


## Objective

To create an asynchronous hangman game with user login validation, and registration. To use a database to store user information and game score information. To have the game display an image of the parts of the hung man as guessed words are incorrect. To display whether their guess was either correct or incorrect.


## Technologies

- C#
- ASP.NET
- Javascript
- Websockets

## Design

### The Homescreen

- ![Game layout](hangman1.png)
### The Game Page

- ![Game layout](hangman2.png)
### The Game Page in the middle of play

- ![Game layout](hangman3.png)
### The Login Page

- ![Game layout](hangman4.png)


## Code snippets of my work


PHP game logic code

` <?php    
    if ($submitbutton){        
        if (($guess > 0) && ($guess < 101)){
            if ($guess < $_COOKIE["rand"]) {
                $cookie_count = ++$_COOKIE["count"];    
                setcookie("count", $cookie_count, time() + (86400 * 30), "/");                
                echo "The number is higher. Try again";
            } else if ($guess > $_COOKIE["rand"]){
                $cookie_count = ++$_COOKIE["count"];    
                setcookie("count", $cookie_count, time() + (86400 * 30), "/");                
                echo "The number is lower. Try again";
            } else {
                $cookie_count = ++$_COOKIE["count"];    
                setcookie("count", $cookie_count, time() + (86400 * 30), "/");
                echo $_COOKIE["rand"] . " " . "is the correct guess! You got it right!<br>"; 
                // server side code
                $servername = "";
                $username = "";
                $db_password = "";
                $db_name = "";

                // Create connection
                $conn = new mysqli($servername, $username, $db_password, $db_name);
                if ($conn->connect_error) {
                    die("Connection failed: " . $conn->connect_error);
                } 
                
                $sqlInsert = "INSERT INTO HighScores (username, score) VALUES ('$user', '$cookie_count')";

                if ($conn->query($sqlInsert) === TRUE) {
                    echo "Score saved. <br><br>";          
                    $sql = "SELECT ID, username, score FROM HighScores ORDER BY score ASC LIMIT 10";
                    $result = $conn->query($sql);
                    if ($result->num_rows > 0) {
                        echo "Top Ten Score's <br><br>";
                        while ($row = $result->fetch_assoc()) {                        
                            echo $row["username"] . " " . $row["score"] . "<br>"; 
                            if($row["username"] == $user && $row["score"] == $cookie_count){
                                $won = TRUE;
                        }                       
                    }                                        
                    } else {
                    echo "Got no results.";
                }      
                } else {
                    echo "Error: " . $sqlInsert . "<br>" . $conn->error;
                }  
                if($won == TRUE){
                    echo "You made it in to the top ten!<br> Your score this round was: " . $cookie_count . "<br><br>";
                }                                                                   
                $conn->close();
                setcookie("rand", $cookie_num, time() - 3600);
                setcookie("count", $cookie_count, time() - 3600);
            }     
        } else {
            echo "Use a number between 1 and 100.";
        }
    }
`

PHP capture user and hash password code


 ` function genRandStr($numLetters){
    $randString="";
    $chars="abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
    for($i=0; $i<$numLetters; $i++){
      $randInt = rand(0,61);
      $randChar = $chars[$randInt];
      $randString = $randString.$randChar;
    }
    return $randString;
  }

  $user = $_POST["User"];
  $pass = $_POST["Pass"];
  $hash = hash('sha256', $pass);
  $salt = genRandStr(16);
  $password = $hash.$salt;
  
  if($user === " " || $password === " "){
    header("Location: ");
  }
  
  // save session vars
  $_SESSION["user"] = $user;
  $_SESSION["pass"] = $pass;
  $_SESSION["salt"] = $salt;

   // server side code
  $servername = "";
  $username = "";
  $db_password = "";
  $db_name = "";
   
  // Create connection
  $conn = new mysqli($servername, $username, $db_password, $db_name);
  if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
  }
  // sql to find matching user and get result
  $sqlFind = "SELECT * FROM Users WHERE username='$user'";
  $result = $conn->query($sqlFind);  


  if ($result->num_rows > 0) {
    while ($row = $result->fetch_assoc()) {
      $dSalt = $row["salt"];
      $pw = $hash.$dSalt;      
      if($user === $row["username"]){
        echo " Sorry that username already exists";        
      }
    }
  } else {
    $sql = "INSERT INTO Users (username, hashPlusSalt, salt) VALUES ('$user', '$password', '$salt')";
    if ($conn->query($sql) === TRUE) {
      echo "New record created successfully <br>"; 
      header("Location: ");       
    } else {
      echo "Error: " . $sql . "<br>" . $conn->error;
  }
  $conn->close();
}
?>`


### Download
- [Hangman](https://github.com/seanafoster/Hangman/archive/refs/heads/master.zip)
