### Introduction

Over the last 20 years from relative obscurity to being all pervasive the internet has changed the course of human interactions with the world. Being connected to the internet is an important pre-requisite for all humans in order to conduct their day to day lives be it financial, health, social and communication. The ability to book tickets without going to the theatre or the ability to send money from one person to the other without going to a bank or the ability to keep in touch with friends or being able to buy products without visiting a store also means that bad people might be looking for an opportunity to steal information such as credit cards, health records, personal information thereby causing a loss to the individual. As developers of web applications, it is important that we are aware of the different kinds of attacks on a web application and how to prevent them.

### Security Breaches
#### Facebook 

In September of 2018, Facebook disclosed that between 30-90 million user access tokens were stolen which gave the hackers access to the users' Facebook accounts and their data that was held because of a bug in web apps **view as** feature. 

#### Panera Bread

Panera Bread is a chain of bakery cafe's in the USA and between August 2017 - April 2018, upto **37 million** customers' personal information was stolen. The names, contact details and partial payment details were freely available in plain text. If the correct URL was used, anyone would be able to access Panera Bread’s customers’ data

#### Canva Data Breach

In May 2019, Canva a graphic-design tool website suffered a data breach that resulted in the loss of 139 million users' personal data. This included customer usernames, real names, email addresses, password and city and country information.

For some more data breaches in 2019, I would recommend you read [this](https://www.securitymagazine.com/articles/91366-the-top-12-data-breaches-of-2019 'Top 12 Security Breaches in 2019').

In all of the above incidents, user information was stolen leading to disclosure of personally identifiable information or financial information and highlights the need for the developers to build secure web applications. In orders to build secure web applications, it is important to understand the different types of threats or vulnerabilities that need to be addressed. 

The impact of a data or security breach can range from outage (having short term revenue impact), loss of trust (leading to long term revenue impact), lawsuits against the company, government actions against the company (in case of compliance violations) and so on.

### Common Security Vulnerabilties

Security has to be addressed at all layers of the web application and includes the network, platform, data and web application levels. This article discusses only the vulnerabilities that need to be addressed at the application level only. 

[![security_sdlc.png](https://i.imgur.com/E7cd583.png)

#### Injection

Inject attacks happen when a code interpreter executes untrusted data that is either given via a form input or by other means. For example, an user can enter SQL code into a form that expects an username. If the form input has not been properly secured and sanitized it could lead to the SQL Code being executed resulting in data loss or data breach. This is commonly know as the **SQL Injection** attack. These attacks are very common because of the prevalence of significant SQL injection vulnerabilities and the database is also the state of the application (all data is stored there).

Assuming we have a query that provides the information of a user given their username and the query is framed as below
```
query = "SELECT * from users where username = '" + username + "';"
```

If the input to the field ```username``` is ```test; delete from users``` this results in the data of the ```users``` table to be deleted.

This can be fixed by making use of **Prepared Statements** with Prepared Queries that allows for binding of variables to the query and all input is sanitized. Here is an example of how it is done in Java

```
...
query = "SELECT * from users where username = ?"
PreparedStatement pstmt = connection.prepareStatement( query );
String user = request.getParameter("username")
pstmt.setString(1, user)
pstmt.executeQuery()
...
```

Refer to [this](https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html 'SQL Injection Prevention') OWASP Cheatsheet on SQL Injection for more information.

#### Broken Authentication

Broken authentication happens mostly due to poor implementation of application functions related to authentication and session management, thus allowing attackers to compromise passwords, keys or session tokens, even going so far as to exploit other implementation flaws to assume users’ identities temporarily or permanently.

This sort of attack is very common and hackers could employ a dictionary based attack or credential stuffing where the previously hacked usernames and passwords from other applications could be used to impersonate the user. 

Another way to exploit broken authentication is using session management attacks. Since HTTP is a stateless protocol, and session keys or access tokens are used to handle session management, this attack will exploit weaknesses in such systems to gain access. A simple example is the case of unexpired session token vulnerability. For instance, if a user accessing an application simply closes the browser, the session may not expire. This would allow an unauthorized party to use the same computer/browser to access the web application, since the original user is still authenticated.

Broken authentication can be fixed by 
* Requiring usage of complex passwords and should be a combination of numbers, characters and special characters.
* Requiring the user to use multi-factor authentication
* Avoiding the usage of session id's in the URL (url rewriting)
* Usage of secure and http cookies
* Rotating a session id after login
* Better session management using timeouts and logging the user after inactivity.

#### Broken Access Control

Access control or **authorization** is a means by which the developer defines policies in the application as to the operations a logged in user can perform. Actions that are destructive or have access to the complete system are granted to some users and not others. A web application’s access control model is closely tied to the content and functions that the site provides. In addition, the users may fall into a number of groups or roles with different abilities or privileges. 

The access control layer of the system generally evolves along with the application and may not have been deliberately designed. In such cases, the access control code may have been inserted all over the code base and over a period of time might become difficult to understand.

A typical example can be when the user modifies the id parameter in a URL(```http://my.insecure.com/users/me?id=abcd```) that fetches user information. The access control should only allow to fetch the users' own information. In case no such policies exist, the application risks allowing the hacker to fetch the data of all users by changing the id. 

As developers we need to make sure that we deny any access by default and use a well defined user access list.

#### Sensitive Data Exposure

Sensitive Data Exposure attacks happen when an application does not sufficiently and adequately protect sensitive information.The data can vary and anything from passwords, session tokens, credit card data to private health data can be exposed. Rather than directly attacking crypto, attackers steal keys, execute man-in-the-middle attacks, or steal clear te xt data off the server, while in transit, or from the user’s client, e.g. browser. Over the last few years, this has been the most common impactful attack. The most common flaw is simply not encrypting sensitive data. 

An example of this kind of attack is when a site does not enforce TLS (https) for all pages, an attacker monitors all network traffic (e.g. at an insecure wireless network), intercepts requests, and steals the user's session cookie. The attacker then replays this cookie and hijacks the user's (authenticated) session, accessing or modifying the user's private data. Instead of the above they could alter all transported data, e.g. the recipient of a money transfer. 

To prevent this kind of an attack the application should 
* make sure to encrypt all data at rest and in transit
* disable caching of data that is sensitive, 
* Store passwords using strong adaptive and salted hashing functions using brcypt or pkdf 
* Ensure up-to-date and strong standard algorithms, protocols, and keys are in place; use proper key management.

#### Using Components With Known Vulnerabilities

There is very little code in the world today that does not depend on other code at some point in time. This could be a 3rd party commercial component or a open source software library. The usage of these components by themselves is not a security problem but if we use these components with known vulnerabilities, could lead to far reaching security breaches as [Equifax](https://www.nbcnews.com/business/consumer/how-did-equifax-hack-even-happen-n801331 'Equifax') found out in July 2017. They did not patch the Apache Struts framework and the hackers utilized this information to steal data from Equifax. So it is important that as developers we are aware of vulnerabilities that have been found in the components and be prepared for patching those components as soon as possible. Don't leave vulnerable software in production.

### Final thoughts

This article is a ring side view of some of the vulnerabilities that can be present in a web application and provides information on some of the most common ones. Web application security is a complex topic and the reader is encouraged to understand more by utilizing the vast information available on the internet.