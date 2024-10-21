import pygame
import random
import time

# Initialize Pygame
pygame.init()

# Set up screen
WIDTH, HEIGHT = 1200, 800
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Empire Rise")

# Define colors
WHITE = (255, 255, 255)
BLACK = (0, 0, 0)
GREEN = (0, 255, 0)

# Load map image
world_map = pygame.image.load("world_map.png")
world_map = pygame.transform.scale(world_map, (WIDTH, HEIGHT))

# Define countries
countries = {
    'USA': {'resources': 100, 'military': 50, 'research': 0, 'alliances': [], 'ai': False},
    'Russia': {'resources': 120, 'military': 60, 'research': 0, 'alliances': [], 'ai': True},
    'China': {'resources': 130, 'military': 55, 'research': 0, 'alliances': [], 'ai': True},
    'Germany': {'resources': 90, 'military': 40, 'research': 0, 'alliances': [], 'ai': True},
}

# Research tree
research_tree = {
    'Economy': {'cost': 20, 'benefit': 'Increase resources production'},
    'Military': {'cost': 30, 'benefit': 'Increase military strength'},
    'Diplomacy': {'cost': 15, 'benefit': 'Better alliances'}
}

player_country = None
game_speed = 1.0  # Game speed modifier

def draw_map():
    screen.blit(world_map, (0, 0))

def choose_country():
    global player_country
    font = pygame.font.SysFont(None, 55)
    text = font.render("Choose Your Country: USA, Russia, China, Germany", True, WHITE)
    screen.blit(text, (150, 50))
    pygame.display.update()

    while player_country is None:
        for event in pygame.event.get():
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_u:
                    player_country = 'USA'
                elif event.key == pygame.K_r:
                    player_country = 'Russia'
                elif event.key == pygame.K_c:
                    player_country = 'China'
                elif event.key == pygame.K_g:
                    player_country = 'Germany'
        time.sleep(0.1)

def manage_country(country):
    font = pygame.font.SysFont(None, 35)
    country_data = countries[country]
    resources = country_data['resources']
    military = country_data['military']
    research_points = country_data['research']

    text = font.render(f"Country: {country} | Resources: {resources} | Military: {military} | Research: {research_points}", True, GREEN)
    screen.blit(text, (20, 20))

def handle_research(country):
    country_data = countries[country]
    font = pygame.font.SysFont(None, 30)
    research_text = font.render("Press E for Economy, M for Military, D for Diplomacy", True, GREEN)
    screen.blit(research_text, (20, 70))

    for event in pygame.event.get():
        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_e and country_data['resources'] >= research_tree['Economy']['cost']:
                country_data['research'] += 1
                country_data['resources'] -= research_tree['Economy']['cost']
            elif event.key == pygame.K_m and country_data['resources'] >= research_tree['Military']['cost']:
                country_data['research'] += 1
                country_data['resources'] -= research_tree['Military']['cost']
            elif event.key == pygame.K_d and country_data['resources'] >= research_tree['Diplomacy']['cost']:
                country_data['research'] += 1
                country_data['resources'] -= research_tree['Diplomacy']['cost']

def update_ai():
    for country, data in countries.items():
        if data['ai']:
            # AI makes random decisions based on resources
            action = random.choice(['research', 'alliance', 'war'])
            if action == 'research' and data['resources'] >= 20:
                data['research'] += 1
                data['resources'] -= 20

def game_loop():
    running = True
    clock = pygame.time.Clock()

    choose_country()

    while running:
        screen.fill(BLACK)
        draw_map()
        manage_country(player_country)
        handle_research(player_country)
        update_ai()

        pygame.display.update()
        clock.tick(60 * game_speed)  # Adjust game speed

        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                running = False

    pygame.quit()

if __name__ == "__main__":
    game_loop()
