import pygame
import time
import random

pygame.init()

# Các biến cơ bản
width = 800
height = 600
block_size = 20
fps = 15

# Màu sắc
white = (255, 255, 255)
black = (0, 0, 0)
red = (255, 0, 0)
green = (0, 255, 0)

# Khởi tạo cửa sổ game
game_display = pygame.display.set_mode((width, height))
pygame.display.set_caption('Rắn')

clock = pygame.time.Clock()

# Hàm vẽ rắn
def snake(block_size, snake_list):
    for x in snake_list:
        pygame.draw.rect(game_display, green, [x[0], x[1], block_size, block_size])

# Hàm hiển thị tin nhắn
def message_to_screen(msg, color):
    font = pygame.font.SysFont(None, 25)
    screen_text = font.render(msg, True, color)
    game_display.blit(screen_text, [width / 2, height / 2])

# Hàm chính
def gameLoop():
    game_exit = False
    game_over = False

    # Khởi tạo vị trí và tốc độ của con rắn
    lead_x = width / 2
    lead_y = height / 2
    lead_x_change = 0
    lead_y_change = 0

    snake_list = []
    snake_length = 1

    # Vị trí thức ăn ban đầu
    rand_food_x = round(random.randrange(0, width - block_size) / 10.0) * 10.0
    rand_food_y = round(random.randrange(0, height - block_size) / 10.0) * 10.0

    while not game_exit:
        while game_over == True:
            game_display.fill(white)
            message_to_screen("Game over! Nhấn C hoặc Q để thoát hoặc nhấn R để chơi lại.", red)
            pygame.display.update()

            for event in pygame.event.get():
                if event.type == pygame.QUIT:
                    game_exit = True
                    game_over = False
                if event.type == pygame.KEYDOWN:
                    if event.key == pygame.K_q or event.key == pygame.K_c:
                        game_exit = True
                        game_over = False
                    elif event.key == pygame.K_r:
                        gameLoop()

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                game_exit = True
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_LEFT:
                    lead_x_change = -block_size
                    lead_y_change = 0
                elif event.key == pygame.K_RIGHT:
                    lead_x_change = block_size
                    lead_y_change = 0
                elif event.key == pygame.K_UP:
                    lead_y_change = -block_size
                    lead_x_change = 0
                elif event.key == pygame.K_DOWN:
                    lead_y_change = block_size
                    lead_x_change = 0

        # Kiểm tra xem con rắn có chạm vào tường không
        if lead_x >= width or lead_x < 0 or lead_y >= height or lead_y < 0:
            game_over = True

        lead_x += lead_x_change
        lead_y += lead_y_change

        game_display.fill(white)
        pygame.draw.rect(game_display, red, [rand_food_x, rand_food_y, block_size, block_size])

        snake_head = []
        snake_head.append(lead_x)
        snake_head.append(lead_y)
        snake_list.append(snake_head)

        if len(snake_list) > snake_length:
            del snake_list[0]

        for each_segment in snake_list[:-1]:
            if each_segment == snake_head:
                game_over = True

        snake(block_size, snake_list)
        pygame.display.update()

        # Kiểm tra xem con rắn có ăn thức ăn không
        if lead_x == rand_food_x and lead_y == rand_food_y:
            rand_food_x = round(random.randrange(0, width - block_size) / 10.0) * 10.0
            rand_food_y = round(random.randrange(0, height - block_size) / 10.0) * 10.0
            snake_length += 1

        clock.tick(fps)

    pygame.quit()
    quit()

gameLoop()
