## Tech Used : Nodejs/Javascript

## Task to be performed

- Given no parameters, return the latest portfolio value per token in USD.
- Given a token, return the latest portfolio value for that token in USD.
- Given a date, return the portfolio value per token in USD on that date.
- Given a date and a token, return the portfolio value of that token in USD on that date.

## Requirements?
- The list of tools required to build and run the app:
- Node v16.x+
- NPM v9.x 

> **Note:** Please add the transactions.csv in the root folder to run this program.
> **Note:** tokenInfo is the command name

#### 1. Without any arguments

```diff
node main.js tokenInfo
```

#### 2. With token as an argument

```diff
node main.js tokenInfo --token BTC
```

or

```diff
node main.js tokenInfo -t BTC
```

#### 3. With date as an argument

```diff
node main.js tokenInfo --date 2019-10-25
```

or

```diff
node main.js tokenInfo -d 2019-10-25
```

> _**Note**: Date need to be on YYYY-MM-DD format_

#### 4. With token and date as an argument

```diff
node main.js tokenInfo --token BTC --date 2019-10-25
```

or

```diff
node main.js tokenInfo -t BTC -d 2019-10-25
```

#### 5. For help

```diff
node main.js help
```

#### 5. For any help related to tokenInfo command

```diff
node main.js tokenInfo --help
```

or

```diff
node main.js tokenInfo -h
```

### 2. Command Line Program

I have used `yargs` module in this node program as its help in creating our own command-line commands in node.js and makes command-line arguments flexible and easy to use. It seems that we will be having four different types of command to solve this task. Command will be on this format.

1. Command without any arguments

2. Command with token as an argument

3. Command with date as an argument

4. Command with both token and date as an arguments


`To make the code more manageable, I have used the command approach as we may need multiple command in the future.`

If token or date is passed as an arguments on tokenInfo command, we will validate them before passing to the main function.

#### 4. Error Handling on different scenarios

##### I. If token name is missing

```js
node main.js tokenInfo --token
```

If user has enabled the --token option but didn't pass the value of the token name. In this situation, we will validate it with in the `check()` function and throw an error `Please enter token name` and program get terminated.

##### II. If date is missing

```
node main.js tokenInfo --date
```

If user has enabled the --date option but didn't pass any date. In this situation, we will validate it with in the `check()` function and throw an error `Please enter date` and program get terminated.

##### III. If invalid date is entered

```js
node main.js tokenInfo --date shailesh
```

Date passed by the user get validate inside the check() function. If it is invalid, error is thrown with message `Please enter valid date` and program get terminated.

##### IV. For invalid token name

We console the error given token not found.

##### V. If there are no transactions on given date

We console the error no any transactions on the given date.

### 5. Storing cryptocompare api key on the .env file

Cryptocompare url and api key are stored on the .env file as it lets us to customize our individual working environment variables. .env file are not committed in the git so secret credentials will be safe from outside user.

### 7. Generating start epoch time and end epoch time for a given date.

For a given date, we need to get it start epoch timestamp and end epoch timestamp as we have only epoch timestamp on the csv file. By default, javascript will return the time in milli second considering the time zone. To convert it into UTC epoch time,timezoneOffset is deducted. startTimestamp and endTimestamp are embedded inside argv.

```js
const dateToEpochTime = date => {
  date = new Date(`${date}T00:00:00`);
  let userTimezoneOffset = date.getTimezoneOffset() * 60 * 1000;
  const startTimestamp =
    parseInt(new Date(date.getTime() - userTimezoneOffset).getTime()) / 1000;
  const endTimestamp =
    parseInt(
      new Date(
        date.getTime() + 24 * 60 * 60 * 1000 - userTimezoneOffset,
      ).getTime(),
    ) / 1000;

  return [startTimestamp, endTimestamp];
};
```

### 8. Getting the balance of token in USD from cryptocompare

I have gone through the cryptocompare API documentation where I found https://min-api.cryptocompare.com/data/price
endpoint need to be used to get the balance of token in USD. I have created free API key to make use of this endpoint in our app which I have stored in .env file. We need to pass three query params in this case fsym, tsyms and api_key. In fsym,we pass the token name, in tsyms we pass the USD as we need to token balance in USD and in api_key we pass the api key that we have created before.

### 9. Adding command in yargs file.

I have added `tokenInfo` command to get the information related to token. In the future, if we need any extra commands on command line program we can add those command on yargs setup and run the respective code when those command are executed with the help of switch...case program. I have list all the available command and validate that command before going to the next step.

### 10. Loading-like feature till we get the result from the CSV.
