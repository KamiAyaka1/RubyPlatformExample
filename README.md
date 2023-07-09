require 'ruby2d'

# Set up the window dimensions
set width: 800
set height: 600

# Create the player
player = Rectangle.new(
  x: 100, y: 500,
  width: 50, height: 50,
  color: 'blue'
)

# Variables for player movement
player_speed = 5
player_jump = false
player_jump_power = 10
player_y_velocity = 0
gravity = 0.5

# Create the platforms
platforms = [
  Rectangle.new(x: 0, y: 550, width: 800, height: 50, color: 'green'),  # Ground platform
  Rectangle.new(x: 200, y: 450, width: 200, height: 20, color: 'green'), # Platform 1
  Rectangle.new(x: 500, y: 350, width: 200, height: 20, color: 'green')  # Platform 2
]

# Game loop
update do
  # Player movement
  if (holding_left = holding?(:left))
    player.x -= player_speed
  end

  if (holding_right = holding?(:right))
    player.x += player_speed
  end

  # Player jumping
  if player_jump && player_y_velocity.zero?
    player_y_velocity -= player_jump_power
  end

  # Apply gravity to the player
  player_y_velocity += gravity
  player.y += player_y_velocity

  # Collision detection
  landed = false
  platforms.each do |platform|
    if player.y + player.height >= platform.y && player.y <= platform.y + platform.height &&
        player.x + player.width >= platform.x && player.x <= platform.x + platform.width
      landed = true
      player_jump = false
      player_y_velocity = 0
      player.y = platform.y - player.height
    end
  end

  # Enable jumping only when landed
  player_jump = landed if landed

  # Quit the game if Esc key is pressed
  close if holding?(:escape)
end

# Enable jumping when spacebar is pressed
on :key_down do |event|
  player_jump = true if event.key == 'space'
end

show
