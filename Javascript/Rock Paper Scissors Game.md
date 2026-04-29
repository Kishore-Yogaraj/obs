What steps do we need for this game?
- When we click a button: (We are creating an algorithm - an algorithm is a set of steps to complete a task and solve a problem)
	- Computer randomly selects a move
	- Compare the moves to get the result
	- Display the result in a popup

Game code
```javascript
<!DOCTYPE html> 
<html>
  <head>
    <title>Rock Paper Scissors</title>
  </head>

  <body>
    <p>Rock Paper Scissors</p>
    <button onclick = "
      const randomNumber = Math.random() //Generates a random number between 0 <= and <1
    
      let computerChoice = '';


      if (randomNumber >= 0 && randomNumber < 1/3) {
        computerChoice = 'rock';
      } else if (randomNumber >= 1/3 &&randomNumber < 2/3) {
        computerChoice = 'paper';
      } else if (randomNumber >= 2/3 && randomNumber < 1) {
        computerChoice = 'scissors';
      }
    
      console.log(computerChoice);

      let result = '';

      if (computerChoice === 'rock') {
        result = 'Tie.';
      } else if (computerChoice === 'paper') {
        result = 'You lose.';
      } else if (computerChoice === 'scissors') {
        result = 'You win.';
      }

      alert(`You picked rock. The computer picked ${computerChoice}. ${result}`);

    ">Rock</button>

    <button onclick = "

      const randomNumber = Math.random() //Generates a random number between 0 <= and <1
    
      let computerChoice = '';


      if (randomNumber >= 0 && randomNumber < 1/3) {
        computerChoice = 'rock';
      } else if (randomNumber >= 1/3 &&randomNumber < 2/3) {
        computerChoice = 'paper';
      } else if (randomNumber >= 2/3 && randomNumber < 1) {
        computerChoice = 'scissors';
      }
    
      console.log(computerChoice);

      let result = '';

      if (computerChoice === 'rock') {
        result = 'You win.';
      } else if (computerChoice === 'paper') {
        result = 'Tie.';
      } else if (computerChoice === 'scissors') {
        result = 'You lose.';
      }

      alert(`You picked paper. The computer picked ${computerChoice}. ${result}`);

  " >Paper</button>


    <button onclick = "

      const randomNumber = Math.random() //Generates a random number between 0 <= and <1
    
      let computerChoice = '';


      if (randomNumber >= 0 && randomNumber < 1/3) {
        computerChoice = 'rock';
      } else if (randomNumber >= 1/3 &&randomNumber < 2/3) {
        computerChoice = 'paper';
      } else if (randomNumber >= 2/3 && randomNumber < 1) {
        computerChoice = 'scissors';
      }
    
      console.log(computerChoice);

      let result = '';

      if (computerChoice === 'rock') {
        result = 'You lose.';
      } else if (computerChoice === 'paper') {
        result = 'You win.';
      } else if (computerChoice === 'scissors') {
        result = 'Tie.';
      }

      alert(`You picked paper. The computer picked ${computerChoice}. ${result}`);
 
    ">Scissors</button>
    <script>

    </script>
  </body>
</html>
```