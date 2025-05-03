import tkinter as tk
import random

class SnakeGame:
    def __init__(self, master):
        self.master = master
        self.master.title("貪食蛇 Snake Game")
        self.width = 400
        self.height = 400
        self.cell_size = 20
        self.canvas = tk.Canvas(master, width=self.width, height=self.height, bg="black")
        self.canvas.pack()

        self.snake = [(5, 5), (4, 5), (3, 5)]  # 初始身體
        self.food = None
        self.direction = "Right"
        self.running = True

        self.master.bind("<KeyPress>", self.change_direction)
        self.spawn_food()
        self.update()

    def draw_cell(self, x, y, color):
        x1 = x * self.cell_size
        y1 = y * self.cell_size
        x2 = x1 + self.cell_size
        y2 = y1 + self.cell_size
        self.canvas.create_rectangle(x1, y1, x2, y2, fill=color, outline="")

    def spawn_food(self):
        while True:
            x = random.randint(0, (self.width // self.cell_size) - 1)
            y = random.randint(0, (self.height // self.cell_size) - 1)
            if (x, y) not in self.snake:
                self.food = (x, y)
                break

    def change_direction(self, event):
        new_dir = event.keysym
        opposites = {"Up": "Down", "Down": "Up", "Left": "Right", "Right": "Left"}
        if new_dir in opposites and opposites[new_dir] != self.direction:
            self.direction = new_dir

    def move_snake(self):
        head_x, head_y = self.snake[0]
        delta = {"Up": (0, -1), "Down": (0, 1), "Left": (-1, 0), "Right": (1, 0)}
        dx, dy = delta[self.direction]
        new_head = (head_x + dx, head_y + dy)

        # 撞牆或自己
        if (new_head in self.snake or
            new_head[0] < 0 or new_head[1] < 0 or
            new_head[0] >= self.width // self.cell_size or
            new_head[1] >= self.height // self.cell_size):
            self.running = False
            return

        self.snake.insert(0, new_head)

        if new_head == self.food:
            self.spawn_food()
        else:
            self.snake.pop()

    def update(self):
        if not self.running:
            self.canvas.create_text(self.width // 2, self.height // 2, text="遊戲結束", fill="red", font=("Arial", 24))
            return

        self.canvas.delete("all")
        self.move_snake()
        for x, y in self.snake:
            self.draw_cell(x, y, "green")
        self.draw_cell(*self.food, color="red")

        self.master.after(100, self.update)  # 遊戲速度（毫秒）

if __name__ == "__main__":
    root = tk.Tk()
    game = SnakeGame(root)
    root.mainloop()
