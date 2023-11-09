# https://youtu.be/zCEQr5c1duM
# robotarm
import pygame
import numpy as np

RED = (255, 0, 0)

FPS = 60   # frames per second

WINDOW_WIDTH = 1400
WINDOW_HEIGHT = 800

def Rmat(degree):
    rad = np.deg2rad(degree) 
    c = np.cos(rad)
    s = np.sin(rad)
    R = np.array([ [c, -s, 0],
                   [s,  c, 0], [0,0,1]])
    return R

def Tmat(tx, ty):
    Translation = np.array( [
        [1, 0, tx],
        [0, 1, ty],
        [0, 0, 1]
    ])
    return Translation
#

def draw(P, H, screen, color=(100, 200, 200)):
    R = H[:2,:2]
    T = H[:2, 2]
    Ptransformed = P @ R.T + T 
    pygame.draw.polygon(screen, color=color, 
                        points=Ptransformed, width=3)
    return
#



def main():
    pygame.init() # initialize the engine

    sound = pygame.mixer.Sound("assets/diyong.mp3")
    screen = pygame.display.set_mode( (WINDOW_WIDTH, WINDOW_HEIGHT) )
    clock = pygame.time.Clock()
    keys = pygame.key.get_pressed()
    w = 300
    h = 40
    X = np.array([ [0,0], [w, 0], [w, h], [0, h] ])
    position = [0, WINDOW_HEIGHT - 200]
    jointangle1 = 45
    jointangle2 = 30
    jointangle3 = 90
    tick = 0
    done = False
    while not done:
        tick += 1
        #  input handling
        for event in pygame.event.get():
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE:
                    jointangle41 == 0
                    jointangle42 == 180
                elif event.key == pygame.K_LEFT:
                    if jointangle3 > 90:
                        jointangle3 +=0
                    else :
                        jointangle3 +=1
                elif event.key == pygame.K_RIGHT:
                    if jointangle3 < -90:
                        jointangle3 -=0
                    else :
                        jointangle3 -=1
            if event.type == pygame.KEYUP:
                if event.key == pygame.K_SPACE:
                    jointangle41 == -45
                    jointangle42 == 225
                    
 
        # drawing
        screen.fill( (200, 254, 219))

        # base
        pygame.draw.circle(screen, (255,0,0), position, radius=3)
        H0 = Tmat(position[0], position[1]) @ Tmat(0, -h)
        draw(X, H0, screen, (0,0,0)) # base

        # arm 1
        H1 = H0 @ Tmat(w/2, 0)  
        x, y = H1[0,2], H1[1,2] # joint position
        H11 = H1 @ Rmat(-90) @ Tmat(0,-h/2)
        pygame.draw.circle(screen, (255,0,0), (x,y), radius=3) # joint position
        "draw(X, H11, screen, (100,100,0)) # arm 1, 90 degree"
        H12 = H11 @ Tmat(0, h/2) @ Rmat(jointangle1) @ Tmat(0, -h/2)    
        draw(X, H12, screen, (200,200,0)) # arm 1, 90 degree

        # arm 2
        H2 = H12 @ Tmat(w, 0) @ Tmat(0, h/2) # joint 2
        x, y = H2[0,2], H2[1,2]
        pygame.draw.circle(screen, (255,0,0), (x,y), radius=3) # joint position
        jointangle2 = 45#
        H21 = H2 @ Rmat(jointangle2) @ Tmat(0, -h/2)
        draw(X, H21, screen, (0,0, 200))
        
        # arm 3 
        H3 = H21@ Tmat(w,0) @ Tmat(0, h/2) @ Rmat(90)
        x, y = H3[0,1], H3[1,2]
        pygame.draw.circle(screen, (0,0,255), (x,y), radius=3)
        
        H31 = H3 @ Rmat(jointangle3) @ Tmat(0, -h/2)
        draw(X, H31, screen, (0,0, 200))

        
        # gripper
        H4 = H31@ Tmat(w,0) @ Tmat(0,h/4) @Rmat(90)
        x,y = H4[0,1], H4[1,2]
        pygame.draw.circle(screen, (0,0,255), (x,y), radius=3)
        jointangle41 = 45
        jointangle42 = -225
        H41 = H4 @ Rmat(jointangle41) @ Tmat(-w/2-10, -h/2+10)
        H42 = H4 @ Rmat(jointangle42) @ Tmat(-w/2-20, -h/2)
        draw(X/2, H41, screen, (0,0, 100))
        draw(X/2, H42, screen, (0,0, 100))
        
        
        
        # pygame.draw.circle(screen, RED, (cx, cy), radius=3)
        # finish
        pygame.display.flip()
        clock.tick(FPS)
    # end of while

    
# end of main()

if __name__ == "__main__":
    main()
