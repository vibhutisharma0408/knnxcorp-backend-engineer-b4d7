# KN-Lang Full Assignments

This repository contains full KN-Lang source code for **Assignment 1** and **Assignment 2** from *KN-Lang Chronicles*.

---

## 📘 Assignment 1 — Text Adventure Game (Full KN-Lang Code)

```knlang
// KN-Lang Chronicles — Assignment 1
// Just Saying: Text Adventure Game

Squad Room {
    Quark name;
    Quark description;
    Quark items = [];
    Quark exits = {};
    Quark isLocked = False;
    Quark lockItem = "";
    Quark isRiddleRoom = False;
    Quark riddleQuestion = "";
    Quark riddleAnswer = "";

    Doodle showDetails() {
        Print("=== " + name + " ===");
        Print(description);
        If (items.length > 0) {
            Print("Items here: " + items.join(", "));
        } Else {
            Print("It's empty... suspiciously empty.");
        }
        If (exits.keys().length > 0) {
            Print("Exits: " + exits.keys().join(", "));
        }
    }

    Doodle interact(player) {
        If (isLocked == True) {
            If (player.inventory.contains(lockItem)) {
                Print("You use the " + lockItem + ". It unlocks!");
                isLocked = False;
            } Else {
                Print("The door seems to want a " + lockItem + ".");
            }
        }

        If (isRiddleRoom == True AND isLocked == True) {
            Print(riddleQuestion);
            Quark answer = Input("Your answer: ");
            If (answer.toLowerCase() == riddleAnswer.toLowerCase()) {
                Print("Correct!");
                isLocked = False;
            } Else {
                Print("Incorrect. Try again.");
            }
        }
    }
}

Squad Player {
    Quark name = "Unnamed Hero";
    Quark inventory = [];
    Quark currentRoom = "Entrance";
    Quark isAlive = True;

    Doodle pickUp(item) {
        inventory.add(item);
        Print("You picked up the " + item + ".");
    }

    Doodle showInventory() {
        If (inventory.length == 0) {
            Print("You have nothing.");
        } Else {
            Print("Inventory: " + inventory.join(", "));
        }
    }
}

Squad GameManager {
    Quark rooms = {};
    Quark player = new Player();
    Quark isRunning = True;

    Doodle setupRooms() {
        Quark entrance = new Room();
        entrance.name = "Entrance";
        entrance.description = "You are at the start.";
        entrance.exits = { "north": "Dungeon" };
        rooms["Entrance"] = entrance;

        Quark dungeon = new Room();
        dungeon.name = "Dungeon";
        dungeon.description = "Spooky place.";
        dungeon.items.add("rusty key");
        dungeon.exits = { "south": "Entrance", "east": "Locked Door" };
        rooms["Dungeon"] = dungeon;

        Quark locked = new Room();
        locked.name = "Locked Door";
        locked.description = "A locked door.";
        locked.isLocked = True;
        locked.lockItem = "rusty key";
        locked.exits = { "west": "Dungeon", "north": "Treasure" };
        rooms["Locked Door"] = locked;

        Quark treasure = new Room();
        treasure.name = "Treasure";
        treasure.description = "You win!";
        rooms["Treasure"] = treasure;
    }

    Doodle startGame() {
        setupRooms();
        player.name = Input("Enter your name: ");
        Print("Welcome " + player.name + "!");

        SpinCycle (isRunning == True) {
            Quark room = rooms[player.currentRoom];
            room.showDetails();
            Quark cmd = Input(">> ");
            handleCommand(cmd);
        }
    }

    Doodle handleCommand(cmd) {
        Quark parts = cmd.split(" ");
        Quark verb = parts[0];

        If (verb == "go") {
            movePlayer(parts[1]);
        } Else If (verb == "pick") {
            pickItem(parts[1]);
        } Else If (verb == "inventory") {
            player.showInventory();
        } Else If (verb == "quit") {
            isRunning = False;
        }
    }

    Doodle movePlayer(direction) {
        Quark room = rooms[player.currentRoom];

        If (room.exits.containsKey(direction) == False) {
            Print("Can't go that way.");
            Return;
        }

        Quark next = rooms[room.exits[direction]];

        If (next.isLocked == True) {
            next.interact(player);
            If (next.isLocked == True) Return;
        }

        player.currentRoom = next.name;

        If (next.name == "Treasure") {
            Print("You found treasure! You win!");
            isRunning = False;
        }
    }

    Doodle pickItem(item) {
        Quark room = rooms[player.currentRoom];

        If (room.items.contains(item)) {
            room.items.remove(item);
            player.pickUp(item);
        } Else {
            Print("Item not found.");
        }
    }
}

Quark game = new GameManager();
game.startGame();


// KN-Lang Chronicles — Assignment 2: Quiz Master

Squad Player {
    Quark name = "Unknown";
    Quark score = 0;
}

Squad Question {
    Quark category;
    Quark difficulty;
    Quark questionText;
    Quark correctAnswer;
}

Squad QuizMaster {
    Quark player = new Player();
    Quark questions = [];
    Quark chosenCategory = "";
    Quark chosenDifficulty = "";

    Doodle setupQuestions() {
        Quark q1 = new Question();
        q1.category = "Science";
        q1.difficulty = "Easy";
        q1.questionText = "What is H2O?";
        q1.correctAnswer = "water";
        questions.add(q1);

        Quark q2 = new Question();
        q2.category = "History";
        q2.difficulty = "Medium";
        q2.questionText = "Who was first President of USA?";
        q2.correctAnswer = "george washington";
        questions.add(q2);
    }

    Doodle start() {
        setupQuestions();
        player.name = Input("Enter name: ");
        chooseCategory();
        chooseDifficulty();
        askQuestions();
        showFinalScore();
    }

    Doodle chooseCategory() {
        Print("Pick category: Science / History");
        chosenCategory = Input("Category: ");
    }

    Doodle chooseDifficulty() {
        Print("Pick difficulty: Easy / Medium / Hard");
        chosenDifficulty = Input("Difficulty: ");
    }

    Doodle askQuestions() {
        SpinCycle (i = 0; i < questions.length; i = i + 1) {
            Quark q = questions[i];
            If (q.category == chosenCategory AND q.difficulty == chosenDifficulty) {
                Print(q.questionText);
                Quark ans = Input("Your answer: ");
                evaluate(q, ans);
            }
        }
    }

    Doodle evaluate(q, ans) {
        If (ans.toLowerCase() == q.correctAnswer.toLowerCase()) {
            Print("Correct!");

            If (q.difficulty == "Easy") player.score += 5;
            If (q.difficulty == "Medium") player.score += 10;
            If (q.difficulty == "Hard") player.score += 15;

        } Else {
            Print("Wrong!");

            If (q.difficulty == "Easy") player.score -= 2;
            If (q.difficulty == "Medium") player.score -= 5;
            If (q.difficulty == "Hard") player.score -= 7;
        }
    }

    Doodle showFinalScore() {
        Print("Final Score: " + player.score);
    }
}

Quark quiz = new QuizMaster();
quiz.start();
