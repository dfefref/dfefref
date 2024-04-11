import pygame
import random

# Khởi tạo Pygame
pygame.init()

# Thiết lập màn hình
SCREEN_WIDTH = 600
SCREEN_HEIGHT = 400
screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Survival Game")

# Màu sắc
WHITE = (255, 255, 255)
RED = (255, 0, 0)

# Lớp nhân vật
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((50, 50))
        self.image.fill(RED)
        self.rect = self.image.get_rect()
        self.rect.center = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2)
        self.speed = 5

    def update(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_LEFT]:
            self.rect.x -= self.speed
        if keys[pygame.K_RIGHT]:
            self.rect.x += self.speed
        if keys[pygame.K_UP]:
            self.rect.y -= self.speed
        if keys[pygame.K_DOWN]:
            self.rect.y += self.speed

        # Giới hạn di chuyển của nhân vật trong màn hình
        self.rect.x = max(0, min(self.rect.x, SCREEN_WIDTH - self.rect.width))
        self.rect.y = max(0, min(self.rect.y, SCREEN_HEIGHT - self.rect.height))

# Lớp vật cản
class Obstacle(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = pygame.Surface((30, 30))
        self.image.fill(WHITE)
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(SCREEN_WIDTH - self.rect.width)
        self.rect.y = random.randrange(SCREEN_HEIGHT - self.rect.height)
        self.speed = random.randint(1, 3)

    def update(self):
        self.rect.y += self.speed
        if self.rect.top > SCREEN_HEIGHT:
            self.rect.y = 0
            self.rect.x = random.randrange(SCREEN_WIDTH - self.rect.width)

# Khởi tạo nhân vật và vật cản
player = Player()
obstacles = pygame.sprite.Group()
for _ in range(10):
    obstacle = Obstacle()
    obstacles.add(obstacle)

# Vòng lặp chính
clock = pygame.time.Clock()
running = True
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # Cập nhật
    player.update()
    obstacles.update()

    # Kiểm tra va chạm
    if pygame.sprite.spritecollide(player, obstacles, False):
        running = False

    # Vẽ màn hình
    screen.fill((0, 0, 0))
    screen.blit(player.image, player.rect)
    obstacles.draw(screen)

    pygame.display.flip()
    clock.tick(60)

pygame.quit()
