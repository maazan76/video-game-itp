# video-game-itp/*
The Game Project
Adding Scrolling
*/

// Character position and game state variables
var gameChar_x;
var gameChar_y;
var floorPos_y;
var isLeft = false;
var isRight = false;
var isFalling = false;
var isPlummeting = false;

// Camera position for scrolling
var cameraPosX = 0;

// Game objects
var collectable = [
    {x_pos: 100, y_pos: 0, size: 50, isFound: false},
    {x_pos: 200, y_pos: 0, size: 50, isFound: false},
    {x_pos: 300, y_pos: 0, size: 50, isFound: false},
    {x_pos: 1000, y_pos: 0, size: 50, isFound: false},
    {x_pos: 1200, y_pos: 0, size: 50, isFound: false},
]

var canyon = {
    x_pos: 600,
    width: 100
};

// Arrays for trees, clouds, and mountains
var trees_x = [];
var clouds = [];
var mountains = [];

function setup() {
    createCanvas(1024, 576);
    floorPos_y = height * 3 / 4;
    gameChar_x = width / 2;
    gameChar_y = floorPos_y;

    // Initialize tree positions
    trees_x = [-200, - 50, 100, 300, 500, 750, 950, 1200, 1400, 1700, 2000];

    // Initialize cloud objects
    clouds = [
        { x: 150, y: 100, size: 50 },
        { x: 400, y: 150, size: 70 },
        { x: 700, y: 120, size: 60 },
        { x: 1200, y: 130, size: 80 },
        { x: -100, y: 120, size: 60 },
        { x: -300, y: 130, size: 80 }
    ];

    // Initialize mountain objects
    mountains = [
        { x: 150, y: floorPos_y, width: 150, height: 175 },
        { x: 220, y: floorPos_y, width: 150, height: 200 },
        { x: 750, y: floorPos_y, width: 150, height: 175 },
        { x: 820, y: floorPos_y, width: 150, height: 200 }
    ];
}

function draw() {
    // Sky and ground
    background('skyblue'); // Fill the sky blue
    noStroke();
    fill('green');
    rect(0, floorPos_y, width, height - floorPos_y); // Draw the ground

    // Update camera position
    cameraPosX = gameChar_x - width / 2;

    // Apply camera transformation
    push();
    translate(-cameraPosX, 0);


    // Draw clouds
    for (var i = 0; i < clouds.length; i++) {
        fill(255);
        ellipse(clouds[i].x, clouds[i].y, clouds[i].size, clouds[i].size);
        ellipse(
            clouds[i].x - clouds[i].size * 0.5,
            clouds[i].y,
            clouds[i].size * 0.8,
            clouds[i].size * 0.8
        );
        ellipse(
            clouds[i].x + clouds[i].size * 0.5,
            clouds[i].y,
            clouds[i].size * 0.8,
            clouds[i].size * 0.8
        );
    }

    // Draw mountains
    for (var i = 0; i < mountains.length; i++) {
        stroke(0);
        fill('brown');
        triangle(
            mountains[i].x,
            mountains[i].y,
            mountains[i].x + mountains[i].width / 2,
            mountains[i].y - mountains[i].height,
            mountains[i].x + mountains[i].width,
            mountains[i].y
        );
    }

     // Draw trees
     for (var i = 0; i < trees_x.length; i++) {
        noStroke();
        fill(139, 69, 19);
        rect(trees_x[i] - 10, floorPos_y - 60, 20, 60); // Tree trunk
        fill(34, 139, 34);
        ellipse(trees_x[i], floorPos_y - 80, 50, 50); // Leaves
        ellipse(trees_x[i] - 15, floorPos_y - 90, 50, 50);
        ellipse(trees_x[i] + 15, floorPos_y - 90, 50, 50);
    }

    // Draw canyon
    fill('grey');
    rect(canyon.x_pos, floorPos_y, canyon.width, height - floorPos_y);

    // Falling into the canyon
    if (
        gameChar_x > canyon.x_pos &&
        gameChar_x < canyon.x_pos + canyon.width &&
        gameChar_y >= floorPos_y
    ) {
        isPlummeting = true;
    }
    if (isPlummeting) {
        gameChar_y += 10;
    }

    // Draw the game character
    fill(200, 150, 150);
    ellipse(gameChar_x, gameChar_y - 30, 20, 20); // Head
    fill(255, 0, 0);
    rect(gameChar_x - 10, gameChar_y - 20, 20, 30); // Body
    fill(0);
    rect(gameChar_x - 7, gameChar_y, 5, 10); // Left foot
    rect(gameChar_x + 2, gameChar_y, 5, 10); // Right foot

    // Draw collectable
    for (let i = 0; i < collectable.length; i++) {
        collectable[i].y_pos = floorPos_y;
        if (!collectable[i].isFound) {
            fill(255, 223, 0);
            ellipse(collectable[i].x_pos, collectable[i].y_pos - 10, collectable[i].size - 15);
            fill(255);
            textSize(12);
            textAlign(CENTER);
            text("$", collectable[i].x_pos, collectable[i].y_pos  - 5);
        }
}

    
    // Remove camera transformation
    pop();

    // Check for collectable item interaction
    for (let i = 0; i < collectable.length; i++) {
        if ( dist(gameChar_x, gameChar_y, collectable[i].x_pos, collectable[i].y_pos) <
            collectable[i].size && collectable[i].isFound == false
        ) {
            collectable[i].isFound = true;
        }
    }

    // Gravity and movement
    if (gameChar_y < floorPos_y && !isPlummeting) {
        gameChar_y += 3; // Falling effect
        isFalling = true;
    } else {
        isFalling = false;
    }

    if (isLeft && !isPlummeting) {
        gameChar_x -= 5;
    }
    if (isRight && !isPlummeting) {
        gameChar_x += 5;
    }
}

function keyPressed() {
    if (!isPlummeting) {
        if (key == 'a') {
            isLeft = true;
        } else if (key == 'd') {
            isRight = true;
        } else if (key == 'w' && !isFalling) {
            gameChar_y -= 100; // Jump
        }
    }
}

function keyReleased() {
    if (key == 'a') {
        isLeft = false;
    } else if (key == 'd') {
        isRight = false;
    }
}
