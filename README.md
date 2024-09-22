# Hammurabi Python Game

## Overview
This repository contains a Python-based Hammurabi game developed using layered architecture. The player assumes the role of Hammurabi, managing the city of Sumeria for five years. Strategic decisions must be made regarding land, grain, and feeding the population, with various random events affecting the outcome.

### Key Features:
- **Layered Architecture:** The project is organized into distinct layers (game logic, UI, exceptions).
- **Input Validation:** Built-in validation of player inputs and game rules, with custom exceptions to ensure fair gameplay.
- **RNG Events:** Random elements like rat infestations and land price fluctuations add unpredictability.
- **Text-Based Interface:** A simple and interactive command-line interface.
- **Unit Testing:** Key game functions are tested using `unittest`.

## Installation

To run the game, ensure you have Python 3.x installed. Clone the repository and navigate to the project folder:

```bash
git clone https://github.com/Chris91ss/Hammurabi-Python-Game.git
cd Hammurabi-Python-Game
```

## Running the Game

To start the game, simply run the following command:

```bash
python3 start.py
```

## Project Structure

```plaintext
├── src
│   ├── exceptions.py      # Custom game exceptions
│   ├── game.py            # Core game logic
│   ├── ui.py              # User interface for text input/output
│   ├── tests.py           # Unit tests for game functionality
│   ├── start.py           # Entry point to launch the game
```

## Game Rules

As Hammurabi, you must:
- Manage the city’s population, grain, and land.
- Buy or sell land based on random price changes.
- Feed the population and prevent starvation.
- Plant grain, balancing between available grain stock and people to work the land.
- Survive random events such as rat infestations and unpredictable harvests.

The game lasts five years. If too many people starve, the game will end early.

## Code Overview

### 1. **Game Logic (`game.py`)**:
   The core logic of the game is in this module. It handles the buying/selling of land, feeding the population, planting grain, random events like rat infestations, and advancing to the next year.

   **Example:**
   ```python
   def advance_to_the_next_year(self, acres_to_buy_or_sell, units_to_feed_population, acres_to_plant):
       self.validator_for_decision(acres_to_buy_or_sell, units_to_feed_population, acres_to_plant)

       self.sell_or_buy_acres(acres_to_buy_or_sell)

       people_fed = units_to_feed_population // 20
       if people_fed > self.population:
           self.population = people_fed
       self.grain_stock -= units_to_feed_population
       if people_fed < self.population // 2:
           return "Game over. People Starved :("

       self.people_starved = self.population - people_fed
       if self.people_starved == 0:
           self.people_came = random.randint(0, 10)
       else:
           self.people_came = 0

       self.population = self.population - self.people_starved + self.people_came

       self.grain_stock -= acres_to_plant
       self.acres_planted = acres_to_plant

       self.land_price = random.randint(15, 25)
       self.harvest_rate = random.randint(1, 6)

       self.grain_stock += self.acres_planted * self.harvest_rate

       self.rats_infestation()

       self.year += 1

       if self.year == 5:
           return "Game Over."

       return "Continue advancing years."
   ```

### 2. **User Interface (`ui.py`)**:
   The `UI` module provides the text-based interface for the game. It prompts the player for inputs, displays reports after each year, and announces the game's end.

   **Example:**
   ```python
   def start(self):
       UI.display_title_screen()
       while self.game.year < 5:
           self.print_report()
           try:
               acres_to_buy_or_sell, units_to_feed_population, acres_to_plant = UI.receive_input()
               game_status = self.game.advance_to_the_next_year(acres_to_buy_or_sell, units_to_feed_population, acres_to_plant)

               if game_status == "Game over. People Starved :(":
                   self.game_over(1)

           except Exception as exceptions:
               print(exceptions)

       self.game_over(0)
   ```

### 3. **Custom Exceptions (`exceptions.py`)**:
   Custom exceptions are raised if a player tries to make an invalid move, such as buying more land than they can afford or planting too much grain.

   **Example:**
   ```python
   class GameException(Exception):
       pass
   ```

### 4. **Unit Tests (`tests.py`)**:
   Tests cover key game mechanics such as advancing through the years and buying or selling land.

   **Example:**
   ```python
   def test_sell_or_buy_acres(self):
       self.game.sell_or_buy_acres(-100)
       self.assertEqual(self.game.acres_of_land, 900)
   ```

## Unit Testing

The game logic is tested using `unittest`. To run the tests, use the following command:

```bash
python3 -m unittest discover
```

## Contributing

Pull requests are welcome. For significant changes, please open an issue first to discuss what you would like to improve.

## License

This project is licensed under the MIT License.
