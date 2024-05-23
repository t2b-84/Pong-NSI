import pygame
import sys

pygame.init()

# Fenetre jeu
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption('Pong Game')

# Couleurs
WHITE, BLACK, RED, GREEN, BLUE, YELLOW = (255, 255, 255), (0, 0, 0), (255, 0, 0), (0, 255, 0), (0, 0, 255), (255, 255, 0)

# Police
myfont = pygame.font.SysFont('monospace', 50)

# Fichier score
def read_scores():
    try:
        with open("scores.txt", "r") as file:
            return [int(score.strip()) for score in file.readlines()]
    except FileNotFoundError:
        return []

def write_scores(scores):
    with open("scores.txt", "w") as file:
        for score in scores:
            file.write(f"{score}\n")

# Classe Raquette
class Paddle:
    def __init__(self, x, y, width, height, color, speed):
        self.x = x
        self.y = y
        self.width = width
        self.height = height
        self.color = color
        self.speed = speed

    def move_left(self):
        if self.x > 0:
            self.x -= self.speed

    def move_right(self):
        if self.x < WIDTH - self.width:
            self.x += self.speed

    def draw(self, screen):
        pygame.draw.rect(screen, self.color, (self.x, self.y, self.width, self.height))

# Jeu
def game(mode):
    radius = 10
    x, y = WIDTH // 2, HEIGHT // 2
    score = 0
    speed = 5
    x_sens = y_sens = 1

    paddle1 = Paddle(WIDTH // 2 - 100, HEIGHT - 20, 200, 20, WHITE, 10)
    paddle2 = Paddle(WIDTH // 2 - 100, 0, 200, 20, WHITE, 10) if mode == "multi" else None

    end = False
    while not end:
        screen.fill(BLACK)

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()

        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT] and paddle1.x > 0:
            paddle1.move_left()
        if keys[pygame.K_RIGHT] and paddle1.x < WIDTH - paddle1.width:
            paddle1.move_right()

        if mode == "multi":
            if keys[pygame.K_a] and paddle2.x > 0:
                paddle2.move_left()
            if keys[pygame.K_d] and paddle2.x < WIDTH - paddle2.width:
                paddle2.move_right()

        x += x_sens * speed
        y += y_sens * speed

        # Collision
        if x >= WIDTH - radius or x <= radius:
            x_sens *= -1

        if mode == "single":
            if y <= radius:
                y_sens *= -1
            if y >= HEIGHT - radius:
                end = True
        else:
            if y <= radius or y >= HEIGHT - radius:
                end = True

        if paddle1.x < x < paddle1.x + paddle1.width and paddle1.y < y + radius < paddle1.y + paddle1.height:
            y_sens *= -1
            score += 1
            if score % 10 == 0:
                speed += 1
            if score % 50 == 0:
                paddle1.width = max(20, paddle1.width - 20)
                paddle1.x = min(WIDTH - paddle1.width, paddle1.x + 10)

        if mode == "multi" and paddle2.x < x < paddle2.x + paddle2.width and paddle2.y < y - radius < paddle2.y + paddle2.height:
            y_sens *= -1
            score += 1
            if score % 10 == 0:
                speed += 1
            if score % 50 == 0:
                paddle2.width = max(20, paddle2.width - 20)
                paddle2.x = min(WIDTH - paddle2.width, paddle2.x + 10)

        pygame.draw.circle(screen, WHITE, (x, y), radius)
        paddle1.draw(screen)
        if mode == "multi":
            paddle2.draw(screen)
        score_text = myfont.render(f"Score: {score}", True, BLUE)
        screen.blit(score_text, (10, 0))

        pygame.display.update()
        pygame.time.delay(10)

    return score

# Compte a rebours
def countdown():
    for i in range(3, 0, -1):
        screen.fill(BLACK)
        count_text = myfont.render(str(i), True, BLUE)
        screen.blit(count_text, (WIDTH // 2 - count_text.get_width() // 2, HEIGHT // 2 - count_text.get_height() // 2))
        pygame.display.update()
        pygame.time.delay(1000)

# Menu
def menu():
    menu_options = ["Mode Solo", "Mode Duo", "Quit"]
    selected_option = 0

    while True:
        screen.fill(BLACK)
        title = myfont.render("Pong Game Menu", True, BLUE)
        screen.blit(title, (WIDTH // 2 - title.get_width() // 2, HEIGHT // 2 - title.get_height() * 2))

        for i, option in enumerate(menu_options):
            color = BLUE if i == selected_option else WHITE
            option_text = myfont.render(option, True, color)
            screen.blit(option_text, (WIDTH // 2 - option_text.get_width() // 2, HEIGHT // 2 + i * 60))

        pygame.display.update()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            elif event.type == pygame.KEYDOWN:
                if event.key == pygame.K_UP:
                    selected_option = (selected_option - 1) % len(menu_options)
                elif event.key == pygame.K_DOWN:
                    selected_option = (selected_option + 1) % len(menu_options)
                elif event.key == pygame.K_RETURN:
                    if selected_option == 0:
                        return "single"
                    elif selected_option == 1:
                        return "multi"
                    elif selected_option == 2:
                        pygame.quit()
                        quit()

# Boucle
while True:
    mode = menu()
    if mode == "single":
        countdown()
        current_score = game(mode="single")
    elif mode == "multi":
        countdown()
        current_score = game(mode="multi")

    best_scores = read_scores()
    best_scores.append(current_score)
    best_scores.sort(reverse=True)
    best_scores = best_scores[:5]
    write_scores(best_scores)

    screen.fill(BLACK)
    game_over_text = myfont.render("Game Over! Best Scores:", True, RED)
    screen.blit(game_over_text, (WIDTH // 2 - game_over_text.get_width() // 2, HEIGHT // 2 - game_over_text.get_height() // 2))
    for i, score in enumerate(best_scores):
        score_text = myfont.render(f"{i + 1}: {score}", True, WHITE)
        screen.blit(score_text, (WIDTH // 2 - score_text.get_width() // 2, HEIGHT // 2 + i * 50 + 50))
    pygame.display.update()

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                quit()
            if event.type == pygame.KEYDOWN and event.key == pygame.K_RETURN:
                break
        else:
            continue
        break

