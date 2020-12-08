# final-project-code 
# first code is the code we used for the pong game. it does not include the added level challenge. 
## the second code will be the code we showed in the video, including the level challenge 

# first code
Class = require 'class'
push = require 'push'

require 'Ball'
require 'Paddle'

WINDOW_WIDTH = 1280
WINDOW_HEIGHT = 720

VIRTUAL_WIDTH = 432
VIRTUAL_HEIGHT = 243

PADDLE_SPEED = 200

function love.load()

    love.graphics.setDefaultFilter('nearest', 'nearest')

    love.window.setTitle('Pong')

    math.randomseed(os.time())

    smallFont = love.graphics.newFont('font.ttf', 8)
    scoreFont = love.graphics.newFont('font.ttf', 32)
    victoryFont = love.graphics.newFont('font.ttf', 24)
    love.graphics.setFont(smallFont)

    sounds = {
        ['paddle_hit'] = love.audio.newSource('paddle_hit.wav', 'static'),
        ['score'] = love.audio.newSource('score.wav', 'static'),
        ['wall_hit'] = love.audio.newSource('wall_hit.wav', 'static')
    }

    push:setupScreen(VIRTUAL_WIDTH, VIRTUAL_HEIGHT, WINDOW_WIDTH, WINDOW_HEIGHT, {
        fullscreen = false,
        vsync = true,
        resizable = true
    })

    player1Score = 0
    player2Score = 0

    servingPlayer = math.random(2) == 1 and 1 or 2

    winningPlayer = 0

    player1 = Paddle(5, 20, 5, 20)
    player2 = Paddle(VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 30, 5, 20)

    ball = Ball(VIRTUAL_WIDTH/2-2, VIRTUAL_HEIGHT/2-2, 4, 4) 
    
    if servingPlayer == 1 then
        ball.dx = 100
    else
        ball.dx = -100
    end

    gameState = 'start'
end 

function love.resize(w, h)
    push:resize(w, h)
end

function love.update(dt)
    if gameState == 'play' then

        if ball:collides(player1) then
            ball.dx = -ball.dx * 1.25
            ball.x = player1.x + 5

            sounds['paddle_hit']:play()

            color1 = math.random(0,255) 
            color2 = math.random(0,255) 
            color3 = math.random(0,255)

            function love.draw()
    
                push:apply('start')

                love.graphics.clear(color1/255, color2/255, color3/255, 255/255)

                love.graphics.setFont(smallFont)

                displayScore()

                if gameState == 'start' then 
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Welcome to Pong!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Play!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'serve' then
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Player " .. tostring(servingPlayer) .. "'s serve!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Serve!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'victory' then
                    -- display victory message
                    love.graphics.setFont(victoryFont)
                    love.graphics.printf("Player " .. tostring(winningPlayer) .. " wins!", 0, 10, VIRTUAL_WIDTH, 'center')
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Press Enter to Serve!", 0, 42, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'play' then
                    -- no UI messages to display in play
                end

                -- render paddle
                player1:render()
                player2:render()

                -- render ball (center)
                ball:render()

                displayFPS()
                            
                push:apply('end')
            end

            if ball.dy < 0 then
                ball.dy = -math.random(10,150)
            else
                ball.dy = math.random(10,150)
            end
        end

        if ball:collides(player2) then
            ball.dx = -ball.dx * 1.25
            ball.x = player2.x - 4

            sounds['paddle_hit']:play()

            color4 = math.random(0,255) 
            color5 = math.random(0,255) 
            color6 = math.random(0,255)

            function love.draw()
    
                push:apply('start') 

                love.graphics.clear(color4/255, color5/255, color6/255, 255/255)

                love.graphics.setFont(smallFont)

                displayScore()

                if gameState == 'start' then 
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Welcome to Pong!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Play!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'serve' then
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Player " .. tostring(servingPlayer) .. "'s serve!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Serve!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'victory' then
                    -- display victory message
                    love.graphics.setFont(victoryFont)
                    love.graphics.printf("Player " .. tostring(winningPlayer) .. " wins!", 0, 10, VIRTUAL_WIDTH, 'center')
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Press Enter to Serve!", 0, 42, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'play' then
                    -- no UI messages to display in play
                end

                -- render paddle
                player1:render()
                player2:render()

                -- render ball (center)
                ball:render()

                displayFPS()
                            
                push:apply('end')
            end

            if ball.dy < 0 then
                ball.dy = -math.random(10,150)
            else
                ball.dy = math.random(10,150)
            end
        end

        if ball.y <= 0 then
            ball.y = 0
            ball.dy = -ball.dy

            sounds['wall_hit']:play()
        end

        if ball.y >= VIRTUAL_HEIGHT - 4 then
            ball.y = VIRTUAL_HEIGHT - 4
            ball.dy = -ball.dy

            sounds['wall_hit']:play()
        end
    end

    if ball.x < 0 then
        player2Score = player2Score + 1
        servingPlayer = 1

        sounds['score']:play()

        ball:reset()
        ball.dx = 100

        if player2Score >= 3 then
            gameState = 'victory'
            winningPlayer = 2
        else
            gameState = 'serve'
        end
    end

    if ball.x > VIRTUAL_WIDTH - 4 then
        player1Score = player1Score + 1
        servingPlayer = 2

        sounds['score']:play()

        ball:reset()
        ball.dx = -100

        if player1Score >= 3 then
            gameState = 'victory'
            winningPlayer = 1
        else
            gameState = 'serve'
        end
    end
    
    -- player 1 movement 

    -- if ball velocity > or < 0 then paddle position = ball position
    if ball.dy < 0 or ball.dy > 0 then
        player1.y = ball.y
    else
        player1.dy = 0
    end

    -- player 2 movement 
    if love.keyboard.isDown('up') then
        player2.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('down') then
        player2.dy = PADDLE_SPEED
    else
        player2.dy = 0
    end

    if gameState == 'play' then
        ball:update(dt)
    end

    player1:update(dt)
    player2:update(dt)
end

function love.keypressed(key)
    if key == 'escape' then
        love.event.quit()
    elseif key == 'enter' or key == 'return' then
        if gameState == 'start' then
            gameState = 'serve'
        elseif gameState == 'victory' then
            gameState = 'start'
            player1Score = 0
            player2Score = 0
        elseif gameState == 'serve' then
            gameState = 'play'
        end
    end
end

function love.draw()
    push:apply('start')

    love.graphics.clear(40/255, 45/255, 52/255, 255/255)

    love.graphics.setFont(smallFont)

    displayScore()

    if gameState == 'start' then 
        love.graphics.setFont(smallFont)
        love.graphics.printf("Welcome to Pong!", 0, 20, VIRTUAL_WIDTH, 'center')
        love.graphics.printf("Press Enter to Play!", 0, 32, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'serve' then
        love.graphics.setFont(smallFont)
        love.graphics.printf("Player " .. tostring(servingPlayer) .. "'s serve!", 0, 20, VIRTUAL_WIDTH, 'center')
        love.graphics.printf("Press Enter to Serve!", 0, 32, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'victory' then
        -- display victory message
        love.graphics.setFont(victoryFont)
        love.graphics.printf("Player " .. tostring(winningPlayer) .. " wins!", 0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.setFont(smallFont)
        love.graphics.printf("Press Enter to Serve!", 0, 42, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'play' then
        -- no UI messages to display in play
    end

    -- render paddle
    player1:render()
    player2:render()

    -- render ball (center)
    ball:render()

    displayFPS()
                
    push:apply('end')
end

function displayFPS()
    love.graphics.setColor(0, 1, 0, 1)
    love.graphics.setFont(smallFont)
    love.graphics.print('FPS: ' .. tostring(love.timer.getFPS()), 40, 20)
    love.graphics.setColor(1, 1, 1, 1)
end

function displayScore()
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH/2-50, VIRTUAL_HEIGHT/3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH/2+30, VIRTUAL_HEIGHT/3)
end


## second code

--[[
    GD50 2018
    Pong Remake
    pong-12
    "The Resize Update"
    Author: Colton Ogden
    cogden@cs50.harvard.edu
    Originally programmed by Atari in 1972. Features two
    paddles, controlled by players, with the goal of getting
    the ball past your opponent's edge. First to 10 points wins.
    This version is built to more closely resemble the NES than
    the original Pong machines or the Atari 2600 in terms of
    resolution, though in widescreen (16:9) so it looks nicer on 
    modern systems.
]]

-- push is a library that will allow us to draw our game at a virtual
-- resolution, instead of however large our window is; used to provide
-- a more retro aesthetic
--
-- https://github.com/Ulydev/push
push = require 'push'

-- the "Class" library we're using will allow us to represent anything in
-- our game as code, rather than keeping track of many disparate variables and
-- methods
Class = require 'class'

-- our Paddle class, which stores position and dimensions for each Paddle
-- and the logic for rendering them
require 'Paddle'

-- our Ball class, which isn't much different than a Paddle structure-wise
-- but which will mechanically function very differently
require 'Ball'

WINDOW_WIDTH = 1280
WINDOW_HEIGHT = 720

VIRTUAL_WIDTH = 432
VIRTUAL_HEIGHT = 243

-- speed at which we will move our paddle; multiplied by dt in update
PADDLE_SPEED = 200

-- level 1 
Easy = false

-- level 2
Medium = false 

--level 3
Hard = false 


--[[
    Runs when the game first starts up, only once; used to initialize the game.
]]
function love.load()
    
    -- set love's default filter to "nearest-neighbor", which essentially
    -- means there will be no filtering of pixels (blurriness), which is
    -- important for a nice crisp, 2D look
    love.graphics.setDefaultFilter('nearest', 'nearest')

    -- set the title of our application window
    love.window.setTitle('Pong')

    -- "seed" the RNG so that calls to random are always random
    -- use the current time, since that will vary on startup every time
    math.randomseed(os.time())

    -- initialize our nice-looking retro text fonts
    smallFont = love.graphics.newFont('font.ttf', 8)
    largeFont = love.graphics.newFont('font.ttf', 16)
    scoreFont = love.graphics.newFont('font.ttf', 32)
    love.graphics.setFont(smallFont)

    -- set up our sound effects; later, we can just index this table and
    -- call each entry's `play` method
    sounds = {
        ['paddle_hit'] = love.audio.newSource('sounds/paddle_hit.wav', 'static'),
        ['score'] = love.audio.newSource('sounds/score.wav', 'static'),
        ['wall_hit'] = love.audio.newSource('sounds/wall_hit.wav', 'static')
    }

    -- initialize window with virtual resolution
    push:setupScreen(VIRTUAL_WIDTH, VIRTUAL_HEIGHT, WINDOW_WIDTH, WINDOW_HEIGHT, {
        fullscreen = false,
        resizable = false,
        vsync = true
    })

    -- initialize score variables, used for rendering on the screen and keeping
    -- track of the winner
    player1Score = 0
    player2Score = 0

    -- either going to be 1 or 2; whomever is scored on gets to serve the
    -- following turn
    servingPlayer = 1

    -- initialize player paddles and ball
    player1 = Paddle(10, 30, 5, 20)
    player2 = Paddle(VIRTUAL_WIDTH - 15, VIRTUAL_HEIGHT - 30, 5, 20)
    ball = Ball(VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)

    gameState = 'start'
end

--[[
    Called by LÖVE whenever we resize the screen; here, we just want to pass in the
    width and height to push so our virtual resolution can be resized as needed.
]]
function love.resize(w, h)
    push:resize(w, h)
end

--[[
    Runs every frame, with "dt" passed in, our delta in seconds 
    since the last frame, which LÖVE2D supplies us.
]]
function love.update(dt)
    if gameState == 'serve' then
        -- before switching to play, initialize ball's velocity based
        -- on player who last scored
        ball.dy = math.random(-50, 50)
        if servingPlayer == 1 then
            ball.dx = math.random(140, 200)
        else
            ball.dx = -math.random(140, 200)
        end
    
    elseif gameState == 'Easy' then 
        if ball:collides(player1) then
            ball.dx = -ball.dx * 1.1
            ball.x = player1.x + 5

            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            sounds['paddle_hit']:play()
        end
        if ball:collides(player2) then
            ball.dx = -ball.dx * 1.1
            ball.x = player2.x - 4

            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            sounds['paddle_hit']:play()
        end
    
    elseif gameState == 'Medium' then 
        if ball:collides(player1) then
            ball.dx = -ball.dx * 1.3
            ball.x = player1.x + 5

            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            sounds['paddle_hit']:play()
        end
        if ball:collides(player2) then
            ball.dx = -ball.dx * 1.3
            ball.x = player2.x - 4

            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            sounds['paddle_hit']:play()
        end

    elseif gameState == 'Hard' then 
        if ball:collides(player1) then
            ball.dx = -ball.dx * 2
            ball.x = player1.x + 5
    
            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end
    
            sounds['paddle_hit']:play()
        end
        if ball:collides(player2) then
            ball.dx = -ball.dx * 2
            ball.x = player2.x - 4
    
            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end
    
            sounds['paddle_hit']:play()
        end   
    
    elseif gameState == 'play' then
        -- detect ball collision with paddles, reversing dx if true and
        -- slightly increasing it, then altering the dy based on the position of collision
        if ball:collides(player1) then
            ball.dx = -ball.dx * 1.55
            ball.x = player1.x + 5

            sounds['paddle_hit']:play()

            color1 = math.random(0,255) 
            color2 = math.random(0,255) 
            color3 = math.random(0,255)

            function love.draw()

                push:apply('start')

                love.graphics.clear(color1/255, color2/255, color3/255, 255/255)

                love.graphics.setFont(smallFont)

                displayScore()

                if gameState == 'start' then 
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Welcome to Pong!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Play!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'serve' then
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Player " .. tostring(servingPlayer) .. "'s serve!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Serve!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'victory' then
                    -- display victory message
                    love.graphics.setFont(victoryFont)
                    love.graphics.printf("Player " .. tostring(winningPlayer) .. " wins!", 0, 10, VIRTUAL_WIDTH, 'center')
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Press Enter to Serve!", 0, 42, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'play' then
                    -- no UI messages to display in play
                end

                -- render paddle
                player1:render()
                player2:render()

                -- render ball (center)
                ball:render()

                displayFPS()

                push:apply('end')
            end
            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            
        end
        if ball:collides(player2) then
            ball.dx = -ball.dx * 1.55
            ball.x = player2.x - 4

            sounds['paddle_hit']:play()

            color4 = math.random(0,255) 
            color5 = math.random(0,255) 
            color6 = math.random(0,255)

            function love.draw()

                push:apply('start') 

                love.graphics.clear(color4/255, color5/255, color6/255, 255/255)

                love.graphics.setFont(smallFont)

                displayScore()

                if gameState == 'start' then 
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Welcome to Pong!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Play!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'serve' then
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Player " .. tostring(servingPlayer) .. "'s serve!", 0, 20, VIRTUAL_WIDTH, 'center')
                    love.graphics.printf("Press Enter to Serve!", 0, 32, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'victory' then
                    -- display victory message
                    love.graphics.setFont(victoryFont)
                    love.graphics.printf("Player " .. tostring(winningPlayer) .. " wins!", 0, 10, VIRTUAL_WIDTH, 'center')
                    love.graphics.setFont(smallFont)
                    love.graphics.printf("Press Enter to Serve!", 0, 42, VIRTUAL_WIDTH, 'center')
                elseif gameState == 'play' then
                    -- no UI messages to display in play
                end

                -- render paddle
                player1:render()
                player2:render()

                -- render ball (center)
                ball:render()

                displayFPS()

                push:apply('end')
            end

            -- keep velocity going in the same direction, but randomize it
            if ball.dy < 0 then
                ball.dy = -math.random(10, 150)
            else
                ball.dy = math.random(10, 150)
            end

            
        end

        -- detect upper and lower screen boundary collision and reverse if collided
        if ball.y <= 0 then
            ball.y = 0
            ball.dy = -ball.dy
            sounds['wall_hit']:play()
        end

        -- -4 to account for the ball's size
        if ball.y >= VIRTUAL_HEIGHT - 4 then
            ball.y = VIRTUAL_HEIGHT - 4
            ball.dy = -ball.dy
            sounds['wall_hit']:play()
        end
        
        -- if we reach the left or right edge of the screen, 
        -- go back to start and update the score
        if ball.x < 0 then
            servingPlayer = 1
            player2Score = player2Score + 1
            sounds['score']:play()

            -- if we've reached a score of 10, the game is over; set the
            -- state to done so we can show the victory message
            if player2Score == 10 then
                winningPlayer = 2
                gameState = 'done'
            else
                gameState = 'serve'
                -- places the ball in the middle of the screen, no velocity
                ball:reset()
            end
        end

        if ball.x > VIRTUAL_WIDTH then
            servingPlayer = 2
            player1Score = player1Score + 1
            sounds['score']:play()
            
            if player1Score == 10 then
                winningPlayer = 1
                gameState = 'done'
            else
                gameState = 'serve'
                ball:reset()
            end
        end
    end

    -- player 1 movement
    -- if ball velocity > or < 0 then paddle position = ball position
    if ball.dy < 0 or ball.dy > 0 then
        player1.y = ball.y
    else
        player1.dy = 0
    end

    -- player 2 movement
    if love.keyboard.isDown('up') then
        player2.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('down') then
        player2.dy = PADDLE_SPEED
    else
        player2.dy = 0
    end

    -- update our ball based on its DX and DY only if we're in play state;
    -- scale the velocity by dt so movement is framerate-independent
    if gameState == 'play' then
        ball:update(dt)
    end

    player1:update(dt)
    player2:update(dt)
end

--[[
    Keyboard handling, called by LÖVE2D each frame; 
    passes in the key we pressed so we can access.
]]
function love.keypressed(key)
    
    -- level 1
    if key == '1' and gameState == 'start' then
        Easy = true
    end 

    -- level 2
    if key == '2' and gameState == 'start' then
        Medium = true
    end 

    -- level 3
    if key == '3' and gameState == 'start' then
        Hard = true
    end 

    if key == 'escape' then
        love.event.quit()
    -- if we press enter during either the start or serve phase, it should
    -- transition to the next appropriate state
    elseif key == 'enter' or key == 'return' or key == '1' or key == '2' or key == '3' then
        if gameState == 'start' then
            gameState = 'serve'
        elseif gameState == 'serve' then
            gameState = 'play'
        elseif gameState == 'done' then
            -- game is simply in a restart phase here, but will set the serving
            -- player to the opponent of whomever won for fairness!
            gameState = 'serve'

            ball:reset()

            -- reset scores to 0
            player1Score = 0
            player2Score = 0

            -- decide serving player as the opposite of who won
            if winningPlayer == 1 then
                servingPlayer = 2
            else
                servingPlayer = 1
            end
        end
    end
end

--[[
    Called after update by LÖVE2D, used to draw anything to the screen, 
    updated or otherwise.
]]
function love.draw()

    push:apply('start')

    -- clear the screen with a specific color; in this case, a color similar
    -- to some versions of the original Pong
    love.graphics.clear(40/255, 45/255, 52/255, 255/255)

    love.graphics.setFont(smallFont)

    displayScore()

    if gameState == 'start' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Welcome to Pong!', 0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to begin!', 0, 20, VIRTUAL_WIDTH, 'center')
        --Levels
        love.graphics.printf('Press 1 to play on Easy Level', 0, 30, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press 2 to play on Medium Level', 0, 40, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press 3 to play on Hard Level', 0, 50, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'serve' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Player ' .. tostring(servingPlayer) .. "'s serve!", 
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to serve!', 0, 20, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'play' then
        -- no UI messages to display in play
    elseif gameState == 'done' then
        -- UI messages
        love.graphics.setFont(largeFont)
        love.graphics.printf('Player ' .. tostring(winningPlayer) .. ' wins!',
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.setFont(smallFont)
        love.graphics.printf('Press Enter to restart!', 0, 30, VIRTUAL_WIDTH, 'center')
    end

    player1:render()
    player2:render()
    ball:render()

    displayFPS()

    push:apply('end')
end

--[[
    Renders the current FPS.
]]
function displayFPS()
    -- simple FPS display across all states
    love.graphics.setFont(smallFont)
    love.graphics.setColor(0, 255, 0, 255)
    love.graphics.print('FPS: ' .. tostring(love.timer.getFPS()), 10, 10)
end

--[[
    Simply draws the score to the screen.
]]
function displayScore()
    -- draw score on the left and right center of the screen
    -- need to switch font to draw before actually printing
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50, 
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
end
