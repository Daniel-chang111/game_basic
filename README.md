from curses import KEY_DOWN
import os
import pygame
pygame.init()

screen_width = 640
screen_height = 480
screen = pygame.display.set_mode((screen_width, screen_height))

pygame.display.set_caption("Seok Pang")

clock = pygame.time.Clock()

current_path = os.path.dirname(__file__) #현재 파일위치 반환
image_path = os.path.join(current_path, "images") #images 폴더위치 반환

#배경만들기
background = pygame.image.load(os.path.join(image_path, "background.png"))
#스테이지 만들기
stage = pygame.image.load(os.path.join(image_path, "stage.png"))
stage_size = stage.get_rect().size
stage_height = stage_size[1]
#캐릭터 만들기
character = pygame.image.load(os.path.join(image_path, "character.png"))
character_size = character.get_rect().size
character_width = character_size[0]
character_height = character_size[1]
character_x_pos = (screen_width / 2 - character_width / 2)
character_y_pos = (screen_height - character_height - stage_height)

character_to_x = 0
character_speed = 5


#무기 만들기
weapon = pygame.image.load(os.path.join(image_path, "weapon.png"))
weapon_size = weapon.get_rect().size
weapon_width = weapon_size[0]

#무기 한번에 여러발 발사가능 -> 리스트화
weapons = []

#무기이동속도
weapon_speed = 10



running = True

while running:
    dt = clock.tick(60)


    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_LEFT:
                character_to_x -= character_speed
            elif event.key == pygame.K_RIGHT:
                character_to_x += character_speed
            elif event.key == pygame.K_SPACE: #무기발사
                weapon_x_pos = (character_x_pos) + (character_width / 2) - (weapon_width / 2)
                weapon_y_pos = character_y_pos
    
                weapons.append([weapon_x_pos, weapon_y_pos]) #무기 여러번 쏘기위해 다시한번 리스트화


        if event.type == pygame.KEYUP:
            if event.key == pygame.K_LEFT or event.key == pygame.K_RIGHT:
                character_to_x = 0
            # if event.key == pygame.K_SPACE:
            #     pass

    character_x_pos += character_to_x

    if character_x_pos < 0:
        character_x_pos = 0
    elif character_x_pos >= screen_width - character_width:
        character_x_pos = screen_width - character_width

    #무기 위치 조정
    #[i for i in weapons]
    weapons = [ [w[0], w[1] - weapon_speed] for w in weapons]  #또 리스트로 감쌓음 # x는 그대로 y에서만 스피드 빼줌
    weapons = [ [w[0], w[1]] for w in weapons if w[1] > 0] #천장에 닿으면 없어지게
    
    #그림 그려지는 순서에따라 먼저 보여줌
    screen.blit(background, (0,0))
    for weapon_x_pos, weapon_y_pos in weapons:
        screen.blit(weapon, (weapon_x_pos, weapon_y_pos))
    screen.blit(stage, (0, screen_height - stage_height))
    screen.blit(character, (character_x_pos, character_y_pos))
    pygame.display.update()

pygame.quit()
