# aris
import pygame
import random

# Configurações do jogo
LARGURA_TELA = 800
ALTURA_TELA = 600
FPS = 60

# Configurações do jogador
LARGURA_JOGADOR = 50
ALTURA_JOGADOR = 50
VELOCIDADE_JOGADOR = 5

# Configurações do asteroide
LARGURA_ASTEROIDE = 50
ALTURA_ASTEROIDE = 50
VELOCIDADE_ASTEROIDE = 5

# Configurações do tiro
LARGURA_TIRO = 10
ALTURA_TIRO = 10
VELOCIDADE_TIRO = 10

class Jogador(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = pygame.Surface((LARGURA_JOGADOR, ALTURA_JOGADOR))
        self.image.fill((0,255,0))
        self.rect = self.image.get_rect()
        self.rect.center = (LARGURA_TELA / 2, ALTURA_TELA - ALTURA_JOGADOR)

    def update(self):
        self.speedx = 0
        tecla = pygame.key.get_pressed()
        if tecla[pygame.K_LEFT]:
            self.speedx = -VELOCIDADE_JOGADOR
        if tecla[pygame.K_RIGHT]:
            self.speedx = VELOCIDADE_JOGADOR
        self.rect.x += self.speedx
        if self.rect.right > LARGURA_TELA:
            self.rect.right = LARGURA_TELA
        if self.rect.left < 0:
            self.rect.left = 0

class Asteroide(pygame.sprite.Sprite):
    def __init__(self):
        pygame.sprite.Sprite.__init__(self)
        self.image = pygame.Surface((LARGURA_ASTEROIDE, ALTURA_ASTEROIDE))
        self.image.fill((255,0,0))
        self.rect = self.image.get_rect()
        self.rect.x = random.randrange(LARGURA_TELA - self.rect.width)
        self.rect.y = random.randrange(-100, -40)
        self.speedy = random.randrange(1, 8)

    def update(self):
        self.rect.y += self.speedy
        if self.rect.top > ALTURA_TELA + 10:
            self.rect.x = random.randrange(LARGURA_TELA - self.rect.width)
            self.rect.y = random.randrange(-100, -40)
            self.speedy = random.randrange(1, 8)

class Tiro(pygame.sprite.Sprite):
    def __init__(self, x, y):
        pygame.sprite.Sprite.__init__(self)
        self.image = pygame.Surface((LARGURA_TIRO, ALTURA_TIRO))
        self.image.fill((255,255,0))
        self.rect = self.image.get_rect()
        self.rect.bottom = y
        self.rect.centerx = x
        self.speedy = -VELOCIDADE_TIRO

    def update(self):
        self.rect.y += self.speedy
        if self.rect.bottom < 0:
            self.kill()

pygame.init()
tela = pygame.display.set_mode((LARGURA_TELA, ALTURA_TELA))
clock = pygame.time.Clock()

todos_sprites = pygame.sprite.Group()
asteroides = pygame.sprite.Group()
tiros = pygame.sprite.Group()
jogador = Jogador()
todos_sprites.add(jogador)
for i in range(8):
    a = Asteroide()
    todos_sprites.add(a)
    asteroides.add(a)

rodando = True
while rodando:
    clock.tick(FPS)
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            rodando = False
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_SPACE:
                tiro = Tiro(jogador.rect.centerx, jogador.rect.top)
                todos_sprites.add(tiro)
                tiros.add(tiro)

    todos_sprites.update()

    acertos = pygame.sprite.groupcollide(asteroides, tiros, True, True)
    for acerto in acertos:
        a = Asteroide()
        todos_sprites.add(a)
        asteroides.add(a)

    colisoes = pygame.sprite.spritecollide(jogador, asteroides, False)
    if colisoes:
        rodando = False

    tela.fill((0,0,0))
    todos_sprites.draw(tela)
    pygame.display.flip()

pygame.quit()
