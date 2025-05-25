# PPS-Project
import pygame
import random

pygame.init()

def show_start_screen():
    title_font = pygame.font.SysFont("Arial", 40, bold=True)
    prompt_font = pygame.font.SysFont("Arial", 24)
    title = title_font.render("Ritesh's Spaceship War", True, WHITE)
    prompt = prompt_font.render("Press SPACE to Start", True, WHITE)

    while True:
        screen.fill(BG_COLOR)
        screen.blit(title, (WIDTH // 2 - title.get_width() // 2, HEIGHT // 2 - 60))
        screen.blit(prompt, (WIDTH // 2 - prompt.get_width() // 2, HEIGHT // 2 + 20))
        pygame.display.update()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                exit()
            keys = pygame.key.get_pressed()
            if keys[pygame.K_SPACE]:
                return

show_start_screen()

while running:
    # Screen setup
    WIDTH, HEIGHT = 500, 700
    screen = pygame.display.set_mode((WIDTH, HEIGHT))
    pygame.display.set_caption("Space Shooter 🚀")

    # Colors
    WHITE = (255, 255, 255)
    RED = (255, 80, 80)
    BLUE = (80, 180, 255)
    BG_COLOR = (20, 20, 40)

    # Fonts & Clock
    font = pygame.font.SysFont("Arial", 28)
    clock = pygame.time.Clock()

    # Ship settings
    ship_width, ship_height = 50, 40
    ship_x = WIDTH // 2
    ship_y = HEIGHT - 80
    ship_speed = 6

    # Bullets
    bullets = []
    bullet_speed = 6

    # Enemies
    enemies = []
    enemy_speed = 2
    enemy_spawn_delay = 50  # higher = less enemies
    frame_count = 0

    score = 0
    running = True

    def draw_ship(x, y):
        pygame.draw.rect(screen, BLUE, (x, y, ship_width, ship_height))

    def draw_bullets():
        for b in bullets:
            pygame.draw.rect(screen, WHITE, b)

    def draw_enemies():
        for e in enemies:
            pygame.draw.rect(screen, RED, e)

    while running:
        screen.fill(BG_COLOR)
        frame_count += 1

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

        # Controls
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and ship_x > 0:
            ship_x -= ship_speed
        if keys[pygame.K_RIGHT] and ship_x < WIDTH - ship_width:
            ship_x += ship_speed
        if keys[pygame.K_SPACE] and frame_count % 10 == 0:
            bullet = pygame.Rect(ship_x + ship_width//2 - 2, ship_y, 4, 10)
            bullets.append(bullet)

        # Bullet movement
        bullets = [b.move(0, -bullet_speed) for b in bullets if b.y > 0]

        # Enemy spawn
        if frame_count % enemy_spawn_delay == 0:
            ex = random.randint(0, WIDTH - 40)
            enemies.append(pygame.Rect(ex, 0, 40, 40))

        # Enemy movement
        enemies = [e.move(0, enemy_speed) for e in enemies if e.y < HEIGHT]

        # Collision
        for bullet in bullets[:]:
            for enemy in enemies[:]:
                if bullet.colliderect(enemy):
                    bullets.remove(bullet)
                    enemies.remove(enemy)
                    score += 1
                    break

        # Game Over check
        for enemy in enemies:
            if enemy.y > HEIGHT - 100:
                msg = font.render("GAME OVER! Score: " + str(score), True, WHITE)
                screen.blit(msg, (WIDTH // 2 - 150, HEIGHT // 2))
                pygame.display.update()
                pygame.time.wait(3000)
                running = False

        # Drawing
        draw_ship(ship_x, ship_y)
        draw_bullets()
        draw_enemies()
        score_text = font.render("Score: " + str(score), True, WHITE)
        screen.blit(score_text, (10, 10))

        pygame.display.update()
        clock.tick(60)

    pygame.quit()
