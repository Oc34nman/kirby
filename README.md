# kirby



import pygame
import random

pygame.init()

screen = pygame.display.set_mode((800, 600))
pygame.display.set_caption("Flappy Bird")
clock = pygame.time.Clock()
font = pygame.font.Font(None, 36)
font2 = pygame.font.Font(None, 72)
pipe_image = pygame.image.load('pipes2.png').convert_alpha()
background_image = pygame.image.load('fruita.jpg').convert_alpha()
#load in image here
pinkGuy = pygame.image.load('kirby.png').convert_alpha()
score = 0




class Bird:
    def __init__(self):
        self.y = 400
        self.velocity = 0 

    def flap(self):
        self.velocity = -3

    def physics(self):
        self.velocity += 0.1
        self.y += self.velocity

    def draw(self):
        pygame.draw.rect(screen, (255, 0, 0), (50, self.y, 30, 30))
        
#animation variable
frameWidth = 160
frameHeight = 160
RowNum = 0
frameNum = 0
ticker = 0
    
bird = Bird()

class Pipe:
    def __init__(self, x):
        self.x = x
        self.height = random.randint(50, 400)
        self.gap = 150
        self.top_pipe = pygame.transform.flip(pipe_image, False, True)
        self.bottom_pipe = pipe_image
        

    def move(self):
        self.x -= 2

    def draw(self):
        top_height = self.height
        bottom_height = 800 - (self.height + self.gap)
        
        #top pipe
        screen.blit(self.top_pipe, (self.x,top_height - self.top_pipe.get_height()))
        #bottom pipe
        screen.blit(self.bottom_pipe, (self.x, self.height + self.gap))
        
pipes = []
spawn_pipe = 0
       
def check_collision(bx, by, px, py):
    #top pipe check
    if bx + 30 +30> px and bx < px + 50 and by +30< py:
        return True
    #bottom pipe check
    if bx + 30+30 > px and bx < px + 50 and by + 30 > py + 150:
        return True
    return False

ticker = 0
running = True
while running: #======================Game Loop+++++++++++++++++++++++++++++++++++++++++
    clock.tick(60)
    
    
    ticker += 1
    if ticker%10 == 0:
        ticker = 0
        score += 1
        frameNum+=1
    if frameNum>3:
        frameNum = 0
        
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False
        if event.type == pygame.MOUSEBUTTONDOWN:
            bird.flap()
           
    bird.physics()

    spawn_pipe += 1
    if spawn_pipe >= 150:
        pipes.append(Pipe(800))
        spawn_pipe = 0

    for pipe in pipes:
        pipe.move()
        if check_collision(50, bird.y, pipe.x, pipe.height):
            running = False

    i = len(pipes) - 1
    while i >= 0:
        if pipes[i].x <= - 50:
            pipes.pop(i)
        i -= 1
    #render section---------------------------------------
    screen.fill((0, 0, 0))
    
    screen.blit(background_image, (0,0))
    
    screen.blit(pinkGuy,(50,bird.y),(frameWidth*frameNum,RowNum*frameHeight, frameWidth,frameHeight))
    score_text = font.render("Score:", True, (255, 255, 255))
    screen.blit(score_text, (650, 20))
    score_text2 = font.render(str(score), True, (255, 255, 255))
    screen.blit(score_text2, (750, 20))

    #bird.draw()

    for pipe in pipes:
        pipe.draw()

    pygame.display.flip()


    
#=========end loop====================================================

text = font2.render("GAME OVER", True, (255, 50, 50))
screen.blit(text, (200, 200))
pygame.display.flip()
pygame.time.delay(2000)
pygame.quit()
