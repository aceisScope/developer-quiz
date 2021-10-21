# Unity Finland - React Developer Quiz

## Q1: What controversies do you see in the following API example?

User object API doc

```
GET /users
POST /users/new
POST /users/:id/update
POST /users/:id/rename
POST /users/:id/update-timezone 
DELETE /users/delete?id=:id
```

Here are some examples of the behavior:

```
POST /users/new HTTP/1.1 {
    "name": "Cthulhu"
  }
HTTP/1.1 200
"Error: Username already exists"
```

### A: 
1. `/new` is reduntant in `POST /users/new` since POST can be used on collection URI for creating new source
2. It's better to use PUT for `POST /users/:id/update`, `POST /users/:id/rename` and `POST /users/:id/update-timezone` since PUT should be used to create or replace the state of the target resource with the state defined by the representation enclosed in the request.
3. `DELETE /users/delete?id=:id` should be `DELETE /users/:id`
4. In the example, with "Error: Username already exists" it shouldn't be 200. The response code should be 4xx instead, e.g. `409: Conflict` or `422 Unprocessable Entity`.

## Q2: What issues do you see in the following CSS file?

```
@import 'fonts.css';

.header { 
  font-size: 20px; 
  color: #ccaadd;
}
.header.large {
  font-size: 24px !important;
  color: #ccaadd; 
}
.lfnt { 
  font-weight: 500; 
  font-size: 30px;
}
div > a > span > .blue {
  color: #00f; 
}
.box {
  margin-top: 3; 
  margin-bottom: 3; 
  margin-left: 3; 
  margin-right: 3;
}
```

### A:
1. `color: #ccaadd` is redundant in `.header.large` as it inherits `.header`
2.  in `.box` there's no need to define all the margins one by one since they share the same value. 
3.  `>` selector is for element. `div > a > span > .blue` mixed element and class selectors. It should be `div > a > span.blue` 

## Q3: What problems related to password security can you see in the following example?

A web service stores user information in a database and uses passwords for authentication. Here's how the user password storing and authentication is implemented in ruby (the actual data storage and retrieval is outside the scope of the example):

```
require 'digest'

class User
  # Use salted passwords
  PASSWORD_SALT="trustno1"

  # Stored password hash will be accessible through user.hashed_password
  attr_accessor :hashed_password

  # Authenticates user against given password and returns true # the password matches the stored one
  def verify_password(password)
    if hashed_password.nil? || password.nil? 
      false
    else
      User.hash_password(password) == hashed_password end
  end

  # Changes user's password
  def change_password(new_password)
    self.hashed_password = User.hash_password(new_password.to_s)
  end

  # Hashes the input with salt
  def self.hash_password(password) 
    Digest::MD5.hexdigest(password + PASSWORD_SALT)
  end 
end
```

### A:
1. Using the same salt for every password is against the purpose of a salt. It should be randomly generated for every password.
2. MD5 is insecure and shouldn't be used.
3. It's never a good idea to implement your own password authentication. Existing solutions should be used like `ActiveModel::SecurePassword::ClassMethods`.

*(this problem is on stackoverflow https://stackoverflow.com/questions/45265003/password-security-problems)*

## What would you give as a feedback for a pull request including this code?

```
function getDepositHistorySum(user) {
  const deposits = user.transactions.history.deposits; 
  let sum = 0;
  for (var i = 0; i < deposits.length; i += 1) {
    sum += deposits[i].amount;
  }
  return sum;
}
```

### A:
1. Naming convention and general logic is clear
2. `user.transactions.history.deposits` doesn't have any sanity check so it's heavily depending on the input
3. Scope of `var i = 0` is to the whole function body, which is against the purpose. `let i = 0` should be used in this case.
4. Using `reduce` is better than a for-loop.

## Q5: What issues do you see in this JavaScript function?

```
async function getConversations() {
  u = 'https://example.com/api';
  var convsPath = '/conversations';
  let msgsPath = '/messages';
  
  const result = await fetch(u + convsPath)
    .then(response => response.json())
    .then(conversations => {
      let promises = conversations.map(item => {
        const messagesUrl = u + convsPath + '/' + item.id + msgsPath;
        
        return fetch(messagesUrl)
          .then(response => {
            return response.json().then(msgs => {
              item.messages = msgs;
              return item;
            });
          });
      });
      
      return Promise.all(promises);
   });
   
  return result;
}
```

### A:
1. `u` is not defined and also a bad variable naming. Also all the path variables should be `const`.
2. The `then()` chaining for the `fetch()` is wrong since `await` pauses the execution until the promise is fulfilled. The code can be fixed as
   ```
   const response = await fetch(u + convsPath);
   const conversations = await response.json();
   ...
   ```
3. Multil-layer of `then()` callbacks make the code less-readable. It can be fixed like below
   ```
   const result = await Promise.all(
     conversations.map(async (item) => {
       let response = await fetch(u + convsPath + '/' + item.id + msgsPath);
       let messages = await response.json();
       
       item.messages = message;
       return item;
     });
   );
   ```

## Q6: What would you give as a feedback for a pull request including this code?

```
Account.prototype.increaseBalance = function(amount, isCredit) {
  if (!isCredit) {
    this.debitBalance += amount;
  } else {
    this.creditBalance += amount;
  }
};
```

### A:
1. `amount` should be positive by context, but there's no check for it.
2. It seems amount is a `Number` instance. Not a good idea to use `Number` to handle money values.
