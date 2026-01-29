---
title: "PHP Reservation Platform"
date: 2025-05-28T20:13:58+03:00
summary: A simple reservation platform, where users create accounts then select a date and reserve. Made with LAMP stack
tags: ["projects", "PHP", "web"]
draft: false
---




# The scope of the application

I want to make a simple reservation platform, where users create accounts then simply select a date and reserve. For this project I will use the LAMP tech stack.

- **L** -> Linux.
- **A** -> Apache for the server hosting.
- **M** -> MySQL for the database of the application.
- **P** -> PHP for the server side code.


## Use case diagram

- A simple use case diagram of the web app can be seen below

![Use Case Diagram](/images/useCaseDiag.png)
# Sign up process

A user should be able to sign-up using a username and a password. No email use because I want the process to be quick and painless.

The PHP code that makes this possible is the following:

The sequence diagram of the sign up process
![Image](/images/SignUpSeqDiagram.jpg)

- **isUserValid() : bool** function

	First, the database is called.
	```
	require "dbConnect.php";
	$db = getDB();
	```

	A query is then done to find the `$_POST["name"]` in the database
	If not found, returns true and puts the username value `$_SESSION['name'] = $username;`
	, else returns false.

- **isPasswdValid(): bool**

	Checks if the "enter password" and "re-enter password" got the same input

- **insertUser(name, passwd): bool**

	Gets a connection to the database,
	then executes the following query:
	`$query = "INSERT INTO users (name, passwd) VALUES ('$name', '$passwd')";`



# How does a user reserve a day and hour?

At entering the `main.php`, a table is generated with the avalaible hours this week.

Classes diagrams for the main.php file

![Class Diagram](/images/ClassDiagWebApp.jpg)

- In the main.php, a `CliUserInterface` object is created that can generate a day's worth page. When navigating to different dates, the `scheduleSetup` property of the class is modified and another object is created.
- Classes that have 'Html' in their name are just simple html catalogues. They contain the html code for the 'Generators' classes to use. 'Html' classes don't implement any logic.
- The 'Generators' classes implement the actual logic of selecting the needed html code for the situation.

## User's reservations page

The following is the sequence diagram of the process of generating the user's reservations on myReservations.php web page.

![Seq Diagram](/images/userReservationSeqDiagram.png)


 - The diagram doesn't include the process of canceling an appointment, as it is fairly simple:
 - The future reservations are generated as a form which upon submition thorugh the `Cancel reservation` button will delete the reservation from the database.


- Also, the class diagram of the Reservation looks like this:

![Reservation Class Diagram](/images/ReservationsClassDiagram.png)
## Database

- The database is quite simple, with three tables as in the figure below:

![Database diagram](/images/dbDiagram.png)

- Note that the `email` field from the `users` table is currently not used.
- Also, to add more `option` in the `options` table, one can simply add another field to the table and then add a html code for it in the respective php file.
- For the `options` table, if the corresponding `reserved_dates` row is deleted, the  corresponding `options` row is deleted too.

## The code

- The code can be found on [my github](https://github.com/ciupei-timotei-30323/php)


