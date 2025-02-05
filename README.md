pip install pygame
Balls: Use pygame.draw.circle() to render balls. Assign colors for the different balls and keep track of their positions and velocities.\
import pygame
import math
import random

# Initialize pygame
pygame.init()

# Screen dimensions
WIDTH, HEIGHT = 800, 400
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("8 Ball Pool")

# Colors
GREEN = (34, 139, 34)
WHITE = (255, 255, 255)
BALL_COLOR = (255, 215, 0)
CUE_COLOR = (139, 69, 19)

# Ball settings
BALL_RADIUS = 10
balls = []

# Cue Ball position and velocity
cue_ball = {
    "x": WIDTH // 4,
    "y": HEIGHT // 2,
    "vx": 0,
    "vy": 0
}

# Generate positions for other balls in a triangular pattern
def generate_balls():
    x_offset, y_offset = WIDTH * 3 // 4, HEIGHT // 2
    rows = 5
    for i in range(rows):
        for j in range(i + 1):
            x = x_offset + i * 2 * BALL_RADIUS
            y = y_offset + (j - i / 2) * 2 * BALL_RADIUS
            balls.append({"x": x, "y": y, "vx": 0, "vy": 0})

generate_balls()

# Physics settings
FRICTION = 0.99
power = 0

def move_ball(ball):
    ball["x"] += ball["vx"]
    ball["y"] += ball["vy"]
    ball["vx"] *= FRICTION
    ball["vy"] *= FRICTION

def handle_collisions():
    for ball in balls:
        if math.hypot(cue_ball["x"] - ball["x"], cue_ball["y"] - ball["y"]) < 2 * BALL_RADIUS:
            angle = math.atan2(ball["y"] - cue_ball["y"], ball["x"] - cue_ball["x"])
            cue_ball["vx"] = math.cos(angle) * power
            cue_ball["vy"] = math.sin(angle) * power

def draw_pool_table():
    screen.fill(GREEN)
    for ball in balls:
        pygame.draw.circle(screen, BALL_COLOR, (int(ball["x"]), int(ball["y"])), BALL_RADIUS)
    pygame.draw.circle(screen, WHITE, (int(cue_ball["x"]), int(cue_ball["y"])), BALL_RADIUS)

# Main game loop
running = True
while running:
    screen.fill(GREEN)
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mx, my = pygame.mouse.get_pos()
            angle = math.atan2(my - cue_ball["y"], mx - cue_ball["x"])
            cue_ball["vx"] = math.cos(angle) * 5
            cue_ball["vy"] = math.sin(angle) * 5
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                power += 1  # Press space to increase power

    move_ball(cue_ball)
    handle_collisions()
    draw_pool_table()
    
    pygame.display.flip()
    pygame.time.delay(30)

pygame.quit()
