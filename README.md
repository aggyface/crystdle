# CRYSTDLE — Daily Mineral Guessing Game

**CRYSTDLE** is a browser-based daily puzzle game inspired by Wordle and Pokédle, designed for mineralogy enthusiasts and students. Identify a new mystery mineral every day using its chemical and physical properties.

## 💎 Features

-   **Daily Puzzle:** A new mineral to identify every 24 hours, synced for all players.
-   **Property-Based Feedback:** Each guess reveals a row of comparisons:
    -   **Hardness & Specific Gravity:** Arrows indicate if the target value is higher or lower.
    -   **Luster, Crystal System, & Chemical Group:** Color-coded matches (Green/Red).
    -   **Birefringence & Optical Class:** Full optical property comparisons.
-   **Dynamic Hint System:**
    -   **Guess 5:** Reveals the common crystal habit.
    -   **Guess 7:** Reveals common mineral associations.
    -   **Guess 9:** Reveals the etymology of the mineral's name.
-   **Comprehensive Completion Modal:**
    -   Two-column layout featuring high-quality Wikipedia thumbnails.
    -   **Fun Fact:** An interesting tidbit about the mineral.
    -   **Nerd Fact:** Deep-dive technical knowledge (visible on hovering over the mineral name).
    -   **Quick Links:** Direct access to the mineral's page on Mindat.org.
-   **Practice Mode:** "Play a New Game" button to practice with random minerals outside the daily cycle.
-   **"Give Up" Option:** For those stumped by a particularly tricky specimen.

## 🛠️ Tech Stack

-   **Frontend:** Pure HTML5, CSS3 (Vanilla), and Modern JavaScript (ES6+).
-   **Data:** 16-field JSON database of common and significant minerals.
-   **Assets:** Dynamic images fetched via the Wikipedia API.
-   **No Backend Required:** Runs entirely in the browser as a static site.

## 🚀 How to Play

1.  Open `index.html` in any modern web browser.
2.  Type a mineral name into the search bar.
3.  Use the autocomplete suggestions to select a valid mineral.
4.  Analyze the feedback to narrow down your next guess.
5.  Identify the mineral in as few guesses as possible!

## 📜 License

This project is for educational and entertainment purposes. Mineral data is sourced from various public mineralogical databases.
