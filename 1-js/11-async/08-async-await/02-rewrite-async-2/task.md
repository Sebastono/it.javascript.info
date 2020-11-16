
# Riscrivere "rethrow"  con async/await

<<<<<<< HEAD
Sotto puoi trovare l'esempio "rethrow" dal capitolo <info:promise-chaining>. Riscriverlo usando `async/await` invece di `.then/catch`.
=======
Below you can find the "rethrow" example. Rewrite it using `async/await` instead of `.then/catch`.
>>>>>>> 99e59ba611ab11319ef9d0d66734b0bea2c3f058

Sbarazzarsi della ricorsione a favore di un ciclo in `demoGithubUser`: con `async/await` diventa facile da fare.

```js run
class HttpError extends Error {
  constructor(response) {
    super(`${response.status} for ${response.url}`);
    this.name = 'HttpError';
    this.response = response;
  }
}

function loadJson(url) {
  return fetch(url)
    .then(response => {
      if (response.status == 200) {
        return response.json();
      } else {
        throw new HttpError(response);
      }
    });
}

// Ask for a user name until github returns a valid user
function demoGithubUser() {
  let name = prompt("Enter a name?", "iliakan");

  return loadJson(`https://api.github.com/users/${name}`)
    .then(user => {
      alert(`Full name: ${user.name}.`);
      return user;
    })
    .catch(err => {
      if (err instanceof HttpError && err.response.status == 404) {
        alert("No such user, please reenter.");
        return demoGithubUser();
      } else {
        throw err;
      }
    });
}

demoGithubUser();
```
