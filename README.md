# Snake-Game
Snake Game test 
import pygame
import random

# Initialize Pygame
pygame.init()

# Screen dimensions
WIDTH = 800
HEIGHT = 600
GRID_SIZE = 20
GRID_WIDTH = WIDTH // GRID_SIZE
GRID_HEIGHT = HEIGHT // GRID_SIZE

# Colors
BLACK = (0, 0, 0)
WHITE = (255, 255, 255)
GREEN = (0, 255, 0)
RED = (255, 0, 0)

# Create the screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Snake Game')

# Clock to control game speed
clock = pygame.time.Clock()

class Snake:
    def __init__(self):
        self.body = [(GRID_WIDTH // 2, GRID_HEIGHT // 2)]
        self.direction = (1, 0)
        self.grow = False

    def move(self):
        head = self.body[0]
        new_head = (head[0] + self.direction[0], head[1] + self.direction[1])
        
        # Add new head
        self.body.insert(0, new_head)
        
        # Remove tail if not growing
        if not self.grow:
            self.body.pop()
        else:
            self.grow = False

    def grow_snake(self):
        self.grow = True

    def check_collision(self):
        head = self.body[0]
        
        # Wall collision
        if (head[0] < 0 or head[0] >= GRID_WIDTH or 
            head[1] < 0 or head[1] >= GRID_HEIGHT):
            return True
        
        # Self collision
        if head in self.body[1:]:
            return True
        
        return False

    def draw(self, surface):
        for segment in self.body:
            rect = pygame.Rect(segment[0] * GRID_SIZE, segment[1] * GRID_SIZE, 
                               GRID_SIZE - 1, GRID_SIZE - 1)
            pygame.draw.rect(surface, GREEN, rect)

class Food:
    def __init__(self, snake_body):
        self.position = self.generate_position(snake_body)

    def generate_position(self, snake_body):
        while True:
            x = random.randint(0, GRID_WIDTH - 1)
            y = random.randint(0, GRID_HEIGHT - 1)
            if (x, y) not in snake_body:
                return (x, y)

    def draw(self, surface):
        rect = pygame.Rect(self.position[0] * GRID_SIZE, 
                           self.position[1] * GRID_SIZE, 
                           GRID_SIZE - 1, GRID_SIZE - 1)
        pygame.draw.rect(surface, RED, rect)

def main():
    snake = Snake()
    food = Food(snake.body)
    score = 0
    game_over = False

    # Game loop
    while not game_over:
        # Event handling
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_over = True
            
            # Handle key presses for direction
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_UP and snake.direction != (0, 1):
                    snake.direction = (0, -1)
                elif event.key == pygame.K_DOWN and snake.direction != (0, -1):
                    snake.direction = (0, 1)
                elif event.key == pygame.K_LEFT and snake.direction != (1, 0):
                    snake.direction = (-1, 0)
                elif event.key == pygame.K_RIGHT and snake.direction != (-1, 0):
                    snake.direction = (1, 0)

        # Move snake
        snake.move()

        # Check for collision with walls or self
        if snake.check_collision():
            game_over = True

        # Check for food collision
        if snake.body[0] == food.position:
            snake.grow_snake()
            score += 1
            # Generate new food
            food = Food(snake.body)

        # Clear screen
        screen.fill(BLACK)

        # Draw game objects
        snake.draw(screen)
        food.draw(screen)

        # Update display
        pygame.display.flip()

        # Control game speed
        clock.tick(10)  # 10 frames per second

    # Game over screen
    screen.fill(BLACK)
    font = pygame.font.Font(None, 74)
    text = font.render(f'Score: {score}', True, WHITE)
    text_rect = text.get_rect(center=(WIDTH//2, HEIGHT//2))
    screen.blit(text, text_rect)
    pygame.display.flip()

    # Wait before closing
    pygame.time.wait(2000)

    # Quit Pygame
    pygame.quit()

# Run the game
if __name__ == "__main__":
    main()
