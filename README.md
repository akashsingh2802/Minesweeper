import tkinter as tk
import random

# Game settings
GRID_SIZE = 8
MINES_COUNT = 10
CELL_SIZE = 30

class Cell:
    def __init__(self, master, x, y):
        self.master = master
        self.button = tk.Button(master, width=2, height=1, command=self.reveal)
        self.button.bind("<Button-3>", self.flag)
        self.button.grid(row=x, column=y)
        self.x = x
        self.y = y
        self.is_mine = False
        self.is_revealed = False
        self.is_flagged = False
        self.adjacent_mines = 0

    def reveal(self):
        if self.is_flagged or self.is_revealed:
            return
        self.is_revealed = True
        if self.is_mine:
            self.button.config(text="💣", bg="red")
            self.master.game_over()
        else:
            self.button.config(text=str(self.adjacent_mines) if self.adjacent_mines > 0 else "", bg="lightgrey")
            if self.adjacent_mines == 0:
                self.master.reveal_adjacent(self.x, self.y)
            self.master.check_win()

    def flag(self, event):
        if self.is_revealed:
            return
        self.is_flagged = not self.is_flagged
        self.button.config(text="🚩" if self.is_flagged else "")

class Minesweeper(tk.Tk):
    def __init__(self):
        super().__init__()
        self.title("Minesweeper")
        self.cells = [[Cell(self, x, y) for y in range(GRID_SIZE)] for x in range(GRID_SIZE)]
        self.place_mines()
        self.calculate_adjacency()

    def place_mines(self):
        positions = random.sample(range(GRID_SIZE * GRID_SIZE), MINES_COUNT)
        for pos in positions:
            x, y = divmod(pos, GRID_SIZE)
            self.cells[x][y].is_mine = True

    def calculate_adjacency(self):
        for x in range(GRID_SIZE):
            for y in range(GRID_SIZE):
                if self.cells[x][y].is_mine:
                    continue
                count = 0
                for dx in [-1, 0, 1]:
                    for dy in [-1, 0, 1]:
                        nx, ny = x + dx, y + dy
                        if 0 <= nx < GRID_SIZE and 0 <= ny < GRID_SIZE:
                            if self.cells[nx][ny].is_mine:
                                count += 1
                self.cells[x][y].adjacent_mines = count

    def reveal_adjacent(self, x, y):
        for dx in [-1, 0, 1]:
            for dy in [-1, 0, 1]:
                nx, ny = x + dx, y + dy
                if 0 <= nx < GRID_SIZE and 0 <= ny < GRID_SIZE:
                    cell = self.cells[nx][ny]
                    if not cell.is_revealed and not cell.is_mine:
                        cell.reveal()

    def game_over(self):
        for row in self.cells:
            for cell in row:
                if cell.is_mine:
                    cell.button.config(text="💣", bg="red")
        tk.messagebox.showinfo("Game Over", "You clicked on a mine!")
        self.destroy()

    def check_win(self):
        for row in self.cells:
            for cell in row:
                if not cell.is_mine and not cell.is_revealed:
                    return
        tk.messagebox.showinfo("You Win!", "Congratulations! You cleared the minefield.")
        self.destroy()

if __name__ == "__main__":
    app = Minesweeper()
    app.mainloop()
# Minesweeper
Game of algorithms and probability.
