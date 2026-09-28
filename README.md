import pygame
import math
import random
pygame.init()
WIDTH = 800
HEIGHT = 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Neon Particle Heart")
clock = pygame.time.Clock()
COLORS = [
    (255, 0, 100),      
    (255, 0, 255),      
    (150, 0, 255),      
    (0, 100, 255),      
    (0, 255, 255),      
    (0, 255, 100),      
    (100, 255, 0),      
    (255, 255, 0),     
    (255, 100, 0),      
    (255, 30, 30)      
]
main_color = random.choice(COLORS)
CX = WIDTH // 2
CY = HEIGHT // 2 + 20
SCALE = 15
def heart(t):
    x = 16 * math.sin(t) ** 3
    y = (
        13 * math.cos(t)
        - 5 * math.cos(2 * t)
        - 2 * math.cos(3 * t)
        - math.cos(4 * t)
    )
    return (
        CX + x * SCALE,
        CY - y * SCALE
    )
class Particle:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.speed_x = random.uniform(0.2, 2.5)
        self.speed_y = random.uniform(-1.8, 1.8)
        self.size = random.uniform(1, 2.5)
        self.life = random.randint(40, 120)
        self.max_life = self.life
        self.color = random.choice(COLORS)
    def update(self):
        self.x += self.speed_x
        self.y += self.speed_y
        self.speed_y += random.uniform(-0.02, 0.02)
        self.life -= 1
    def draw(self):
        if self.life <= 0:
            return
        pygame.draw.circle(
            screen,
            self.color,
            (int(self.x), int(self.y)),
            max(1, int(self.size))
        )
particles = []
trail = []
t = math.pi * 1.15
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_ESCAPE:
                running = False
    screen.fill((2, 2, 5))
    x, y = heart(t)
    trail.append((x, y))
    if len(trail) > 500:
        trail.pop(0)
    for i in range(12):
        px = x + random.uniform(-5, 5)
        py = y + random.uniform(-5, 5)
        particles.append(
            Particle(px, py)
        )
    if len(trail) > 2:
        glow = pygame.Surface(
            (WIDTH, HEIGHT),
            pygame.SRCALPHA
        )
        pygame.draw.lines(
            glow,
            (
                main_color[0],
                main_color[1],
                main_color[2],
                30
            ),
            False,
            trail,
            25
        )
        pygame.draw.lines(
            glow,
            (
                main_color[0],
                main_color[1],
                main_color[2],
                60
            ),
            False,
            trail,
            12
        )
        screen.blit(
            glow,
            (0, 0)
        )
        pygame.draw.lines(
            screen,
            main_color,
            False,
            trail,
            4
        )
        pygame.draw.lines(
            screen,
            (220, 255, 255),
            False,
            trail,
            1
        )
    for p in particles:
        p.update()
        p.draw()
    particles = [
        p for p in particles
        if p.life > 0
    ]
    pulse = (
        math.sin(
            pygame.time.get_ticks() * 0.01
        ) + 1
    ) / 2
    radius = int(10 + pulse * 15)
    glow2 = pygame.Surface(
        (radius * 4, radius * 4),
        pygame.SRCALPHA
    )
    for r in range(radius * 2, 2, -2):
        alpha = int(
            60 * (1 - r / (radius * 2))
        )
        pygame.draw.circle(
            glow2,
            (
                main_color[0],
                main_color[1],
                main_color[2],
                alpha
            ),
            (radius * 2, radius * 2),
            r
        )
    screen.blit(
        glow2,
        (
            int(x - radius * 2),
            int(y - radius * 2)
        )
    )
    t += 0.025
    if t >= math.pi * 3.1:
        t = math.pi * 1.15
        trail.clear()
        particles.clear()
        main_color = random.choice(COLORS)
    pygame.display.flip()
    clock.tick(60)
pygame.quit()
