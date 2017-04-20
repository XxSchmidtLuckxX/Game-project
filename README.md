# Game-project
Start of something beautiful
import sys, pygame, math
from pygame.locals import *

# set up a bunch of constants
WHITE    = (255, 255, 255)
BLACK    = (  0,   0,   0)
BROWN    = (139,  69,  19)
DARKGRAY = (128, 128, 128)

BGCOLOR = WHITE

WINDOWWIDTH = 960 # width of the program's window, in pixels
WINDOWHEIGHT = 720 # height in pixels

FPS = 30
speed = 5

cannonImg = pygame.image.load('Cannon.jpg')
ENEMY_SHIP = 'Plain_Ship.jpg'
ENEMY_FRIGATE = ''
ENEMY_MEDIUM = ''
ENEMY_GALLEON = ''

# standard pygame setup code
pygame.init()
FPSCLOCK = pygame.time.Clock()
DISPLAYSURF = pygame.display.set_mode((WINDOWWIDTH, WINDOWHEIGHT))
pygame.display.set_caption('Cannon Brawl')

#Starting x and y pos
yPos = WINDOWHEIGHT/2
xPos = WINDOWWIDTH/2

allSpritesGroup = pygame.sprite.Group()  # List of all sprites (enemies, bullets, and playerShip)
enemyGroup = pygame.sprite.Group()  		# List of each enemy in the game
bulletGroup = pygame.sprite.Group()
playerGroup = pygame.sprite.Group()


cannonSurf = pygame.Surface((100, 100))
cannonSurf.fill(BGCOLOR)

cannonSurf.blit(cannonImg, (00, 00))

# create the base cannon image

class Player(pygame.sprite.Sprite):
    health = 25
    speed = 5


    def __init__(self, xPos, yPos):

        self.xPos = xPos
        self.yPos = yPos


    def move(self, xPos, yPos, mousex, mousey, cannonSurf):

        ## This logic identifies where it points

        degrees = getAngle(xPos, yPos, mousex, mousey)
        # rotate a copy of the cannon image and draw it
        rotatedSurf = pygame.transform.rotate(cannonSurf, degrees)
        rotatedRect = rotatedSurf.get_rect()
        rotatedRect.center = (xPos, yPos)


        DISPLAYSURF.blit(rotatedSurf, rotatedRect)


class Bullet(pygame.sprite.Sprite):

    def __init__(self, mouse, xPos, yPos):

        pygame.sprite.Sprite.__init__(self)

        self.image = pygame.Surface([5, 10])
        self.image.fill(BLACK)

        self.mouse_x, self.mouse_y = mouse[0], mouse[1]
        self.player = player

        self.rect = self.image.get_rect()
    def update(self):

        speed = 4
        range = 200
        distance = [self.mouse_x - self.player[0], self.mouse_y - self.player[1]]
        norm = math.sqrt(distance[0] ** 2 + distance[1] ** 2)
        direction = [distance[0] / norm, distance[1 ] / norm]
        bullet_vector = [direction[0] * speed, direction[1] * speed]


        self.rect.x -= bullet_vector[0]
        self.rect.y -= bullet_vector[1]




class Enemy(pygame.sprite.Sprite):
    health = 10
    speed = 5
    def __init__(self):
		# Call the parent class (Sprite) constructor
        pygame.sprite.Sprite.__init__(self)

        self.image = pygame.image.load(ENEMY_SHIP).convert_alpha()
        self.image = pygame.transform.scale(self.image, (50, 50))
        self.rect = self.image.get_rect()
    def getHealth(self, damage):
        self.health -= damage





def getAngle(x1, y1, x2, y2):
    # Return value is 0 for right, 90 for up, 180 for left, and 270 for down (and all values between 0 and 360)
    rise = y1 - y2
    run = x1 - x2
    angle = math.atan2(run, rise) # get the angle in radians
    angle = angle * (180 / math.pi) # convert to degrees
    angle = (angle) % 360 # adjust for a right-facing sprite
    return angle






directionX = 0
directionY = 0
# main application loop
while True:
    cannon = Player(xPos, yPos)
    cannonRect = (cannon.xPos, cannon.yPos)

    print(cannonRect)
    # event handling loop for quit events
    for event in pygame.event.get():
        if event.type == QUIT or (event.type == KEYUP and event.key == K_ESCAPE):
            pygame.quit()
            sys.exit()

        elif event.type == pygame.MOUSEBUTTONDOWN:
            print('click')
            mouse = pygame.mouse.get_pos()
            bullet = Bullet(mouse , cannonRect)
            allSpritesGroup.add(bullet)





                ## event logic to capture when the user presses arrow keys.
        if event.type == KEYDOWN:
            print("trigger")
            if event.key == K_LEFT:
                directionX = -speed
            elif event.key == K_RIGHT:
                directionX = speed
            elif event.key == K_UP:
                directionY = -speed
            elif event.key == K_DOWN:
                 directionY = speed



        if directionX != 0:
            print("move")
    ## Stop after bordedr
        if (xPos <= 0 and directionX < 0) or (xPos > (WINDOWWIDTH - 125) and directionX) > 0:
            directionX = 0
        if (yPos <= 0 and directionY < 0) or (yPos > (WINDOWHEIGHT - 79) and directionY) > 0:
            directionY = 0
        xPos += directionX
        yPos += directionY

    # fill the screen to draw from a blank state
    DISPLAYSURF.fill(BGCOLOR)

    # draw the cannons pointed at the mouse cursor
    mousex, mousey = pygame.mouse.get_pos()



    cannon.move(xPos, yPos, mousex, mousey, cannonSurf)







    # draw the cross hairs over the mouse
    pygame.draw.line(DISPLAYSURF, BLACK, (mousex - 10, mousey), (mousex + 10, mousey))
    pygame.draw.line(DISPLAYSURF, BLACK, (mousex, mousey - 10), (mousex, mousey + 10))

    # draw the border
    pygame.draw.rect(DISPLAYSURF, BLACK, (0, 0, WINDOWWIDTH, WINDOWHEIGHT), 1)

    allSpritesGroup.update()

    pygame.display.update()
    FPSCLOCK.tick(FPS)

