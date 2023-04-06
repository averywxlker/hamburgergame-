import pygame
import sys
import os
pygame.init()

SCREEN_WIDTH = 800
SCREEN_HEIGHT = 600
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
RED = (255, 0, 0)
GREEN = (0, 255, 0)
BLUE = (0, 0, 255)


current_directory = os.getcwd()
print (current_directory)

screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))
pygame.display.set_caption("Build Your Hamburger Game")

hamburger_image = pygame.image.load(current_directory + "\hamburger.png").convert()
lettuce_image = pygame.image.load(current_directory + "\lettuce.png").convert_alpha()
tomato_image = pygame.image.load(current_directory + "\omato.jpg").convert_alpha()
cheese_image = pygame.image.load(current_directory + "\cheese.png").convert_alpha()

class Hamburger(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        self.image = hamburger_image
        self.rect = self.image.get_rect()
        self.rect.center = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2)

class Topping(pygame.sprite.Sprite):
    def __init__(self, image):
        super().__init__()
        self.image = image
        self.rect = self.image.get_rect()
        self.rect.center = (SCREEN_WIDTH // 2, SCREEN_HEIGHT // 2)
        self.dragging = False
        self.offset_x = 0
        self.offset_y = 0

    def update(self):
        if self.dragging:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            self.rect.x = mouse_x - self.offset_x
            self.rect.y = mouse_y - self.offset_y

topping_choices = [
    {"name": "Lettuce", "image": lettuce_image},
    {"name": "Tomato", "image": tomato_image},
    {"name": "Cheese", "image": cheese_image}
]

clock = pygame.time.Clock()

hamburger = Hamburger()
toppings = pygame.sprite.Group()

dragging_topping = None  # added variable to keep track of the topping being dragged

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        elif event.type == pygame.MOUSEBUTTONDOWN:
            for i, topping_choice in enumerate(topping_choices):
                if topping_choice["name"] in ["Lettuce", "Tomato", "Cheese"]:
                    topping = Topping(topping_choice["image"])
                    topping.rect.x = 50 + i * 100
                    topping.rect.y = SCREEN_HEIGHT - 100
                    toppings.add(topping)
                    if topping.rect.collidepoint(event.pos):
                        topping.dragging = True
                        mouse_x, mouse_y = event.pos
                        topping.offset_x = mouse_x - topping.rect.x
                        topping.offset_y = mouse_y - topping.rect.y
                        dragging_topping = topping  # set the dragging topping to the current topping
        elif event.type == pygame.MOUSEBUTTONUP:
            dragging_topping = None  # reset the dragging topping
            for topping in toppings:
                topping.dragging = False

    screen.fill(WHITE)

    toppings.update()

    screen.blit(hamburger.image, hamburger.rect)
    toppings.draw(screen)

    # added conditional to only update the dragging topping
    if dragging_topping:
        dragging_topping.update()

    pygame.display.flip()

    clock.tick(60) 
