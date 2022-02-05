FlowCharts
+ https://app.code2flow.com/VuibfaDQ9l1i
+ https://app.code2flow.com/1Oz3pmsDQCGz
+ https://app.code2flow.com/w5RS5dbV97WN
+ https://app.code2flow.com/P7yc5NdMJuqS
+ https://app.code2flow.com/zslVHmCHJ4w9
+ https://app.code2flow.com/BvCHTueeBHB9

```js
start;
//define a map loader function to parse maps from files or strings
function LoadMap() {
  [netData] = fetchmapfromserver();
  index = 0;
  while(netData.length != 0) {
    [split] = netData.indexOf('\n');
    line = netData.slice(0, split);
    mapdata[index] = line.split(',');
    for (block in mapdata[index]) {
      enemy.push(block);
    }
    [netData] = netData.slicetoend(split);
    index++;
  }
}
//Scenes
function MainMenu() {
  AddButton("Play");
  AddButton("Map Maker");
  AddButton("Instructions");
}
function InstructionsMenu () {
  buttons["InstructionsBack"] = AddButton("Back");
  DrawText(btnXpos, "Controls\nW: Forward\nS: Reverse\na: Rotate Left\nd: Rotate Right\nRMB: Shoot\nSpace: Shoot")
}
function DrawMap() {
  boxWidth = GraphicsWindow.Width/Array.GetItemCount(worldMap);
  boxHeight = GraphicsWindow.Height/Array.GetItemCount(worldMap[1]);
  for (x = 0; x < Array.GetItemCount(worldMap)-1; x++) {
    for (y = 0; y < Array.GetItemCount(worldMap[1])-1; y++) {
      if (/mouse clicked square/) cycle box;
      GraphicsWindow.FillRectangle(x*boxWidth,y*boxHeight,boxWidth,boxHeight);
      match (worldMap[x][y]) {
        0 => [BrushColor] = "#555";
        1 => [BrushColor] = "#f00";
        2 => [BrushColor] = "#0f0";
        3 => [BrushColor] = "#00f";
        4 => [BrushColor] = "#fff";
        9 => GraphicsWindow.DrawResizedImage(texture,x*boxWidth,y*boxHeight,boxWidth,boxHeight);
        _ => [BrushColor] = "#ff0";
      }
      GraphicsWindow.FillRectangle(x*boxWidth,y*boxHeight,boxWidth,boxHeight);
      GraphicsWindow.DrawRectangle(x*boxWidth,y*boxHeight,boxWidth,boxHeight);
    }
  }
}
//GAME LOOP
while (true) {
  start = Clock.ElapsedMilliseconds;
  if (location == "Game") {
    UpdateGamePlay();
    if (Update == true) {
      createframe();
      UpdateFrame();
      DrawText("fps");
    }
    frameCount = frameCount + 1;
  } else if (location == "MapEditor") {
    DrawMap();
  } else if (location == "MainMenu" and Update == true) {
    MainMenu();
  } else if (location == "Instructions" and update == true) {
    InstructionsMenu();
  }
  Update = "False";
  delay = 20 - (Clock.ElapsedMilliseconds - start);
  if (delay > 0) {
    Program.Delay(delay);
  }
}
//SUBROUTINES
function Initialise() {
  //First Run Download Stuff from the server
  [files] = fetchlistoffilesindir();
  if (!Array.ContainsValue(files, Program.Directory + "\Background.mp3")) {
    CopyFile(DownloadFile("https://SmallBasicServer.spotandjake.repl.co/Background.mp3"),Program.Directory + "\Background.mp3");
  }
  if (!Array.ContainsValue(files, Program.Directory + "\Injured.mp3")) {
    CopyFile(DownloadFile("https://SmallBasicServer.spotandjake.repl.co/Injured.mp3"),Program.Directory + "Injured.mp3");
  }
  if (!Array.ContainsValue(files, Program.Directory + "\Shoot.wav")) {
   CopyFile(DownloadFile("https://SmallBasicServer.spotandjake.repl.co/Shoot.wav"),Program.Directory + "Shoot.wav");
  }
  //Register events
  GraphicsWindow.KeyDown = OnKeyDown;
  GraphicsWindow.KeyUp = OnKeyUp;
  GraphicsWindow.MouseDown = OnMouseDown;
  Controls.ButtonClicked = OnControlClick;
  Timer.Tick = SoundEngine; //run this on the timer event to determine when the sound is over without pausing the entire program(i do not run the main loop on the timer as it can cause issues related to multithreading, not to mention performance do to an issue called reentrency which is the process of the timer being called before the last frame being finished)
  // define varibles
  //Do Setup
  GraphicsWindow.Width = width;
  GraphicsWindow.Height = height+scoreboardHeight;
  GraphicsWindow.FillRectangle(0, 0, width, height);
  texture = LoadImage("https://smallbasicserver.spotandjake.repl.co/enemy.png");
  texture_gun[0] = LoadImage("https://smallbasicserver.spotandjake.repl.co/Gun-1/1.png");
  texture_gun[1] = LoadImage("https://smallbasicserver.spotandjake.repl.co/Gun-1/2.png");
  texture_gun[2] = LoadImage("https://smallbasicserver.spotandjake.repl.co/Gun-1/3.png");
  texture_gun[3] = LoadImage("https://smallbasicserver.spotandjake.repl.co/Gun-1/4.png");
  LoadMap();
  location = "MainMenu";
}
function UpdateGamePlay {
  if (keys["Up"] == true ) moveforward();
  if (keys["Down"] == true) movebackward();
  if (keys["Right"] == true) { 
    dir = dir * Math.cos(-rotSpeed) - dir * Math.sin(-rotSpeed);
    plane = plane * Math.cos(-rotSpeed) - plane * Math.sin(-rotSpeed);
  } 
  if (keys["Left"] == true) { 
    dir = dir * Math.cos(rotSpeed) - dir * Math.sin(rotSpeed);
    plane = plane * Math.cos(rotSpeed) - plane * Math.sin(rotSpeed);
  } 
  if (mouseDown == true) { 
    shoot = 5;
    shots[Math.Floor(posX)+"x"+Math.Floor(posY)]["Direction"] = new Vector2(dirX, dirY);
    shots[Math.Floor(posX)+"x"+Math.Floor(posY)]["Position"] = new Vector2(posX, posY);
  }
  for (enemy in enmy) {
    dist = Math.SquareRoot(Math.Power(posX-enmyPosX, 2) + Math.Power(posY-enmyPosY, 2));
    if (dist < 2 ) health--;
    else if (dist < 10) enmyPos = Math.floor(enmyPos - (enmyPos - pos) * moveSpeed);
    else {
      match (Math.GetRandomNumber(20)) {
        1 => enemy["Position"] = Math.floor(enemy["Position"] + enemy["Direction"] * moveSpeed);
        2 => enemy["Position"] = Math.floor(enemy["Position"] - enemy["Direction"] * moveSpeed);
        3 => enemy["Direction"] = enemy["Direction"] * Math.cos(-rotSpeed) - enemy["Direction"] * Math.sin(-rotSpeed);
        4 => enemy["Direction"] = enemy["Direction"] * Math.cos(rotSpeed) - enemy["Direction"] * Math.sin(rotSpeed);
      } 
    }
  }
  for (tmp_bullet in shots) {
    tmp_bullet["Position"] = tmp_bullet["Position"] + (tmp_bullet["Direction"]*tmp_bullet["Speed"]);
    if (worldMap[Math.Floor(bulletX)][Math.Floor(bulletY)] = 0) { 
      if (worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 8) { 
        worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 0;
      }
      if (tmp_bullet["Speed"] <> 0) { 
        shots[Math.Floor(bulletX)+"x"+Math.Floor(bulletY)] = tmp_bullet;
        worldMap[Math.Floor(tmp_bullet["Position"][0])][Math.Floor(tmp_bullet["Position"][1])] = 8;
      } else if (worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 8 ) {
        worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 0;
      } 
    } else if ( worldMap[Math.Floor(bulletX)][Math.Floor(bulletY)] = 9 And tmp_bullet["parent"] = "Player" ) { 
      newHealth = currenthealth - 10;
      if (newHealth = 0) delete enmy[Math.Floor(bulletX) +"x" + Math.Floor(bulletY)];
      else enmy[Math.Floor(bulletX) +"x" + Math.Floor(bulletY)]["Health"] = newHealth;
    } else if  Math.Floor(bulletX) = Math.floor(posX) And Math.Floor(bulletY) = Math.floor(posY) And tmp_bullet["parent"] = "Enemy") { 
      health--;
    } else if (worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 8) {
      worldMap[Math.Floor(shot["Position"][0])][Math.Floor(shot["Position"][1])] = 0;
    }
  }
}
function createframe () {
  for (x = 0; x < width; x += 10) {
    cameraX = 2 * x / width - 1;
    rayDir = dir + plane * camera;
    map = Math.floor(pos);
    deltaDist = Math.abs(1 / rayDir);
    if (rayDir < 0) {
      [step] = -1;
      sideDist = (pos - map) * deltaDist;
    } else {
      [step] = 1;
      sideDist = (map + 1.0 - pos) * deltaDist;
    } 
    while (!hit) {
      if (sideDistX < sideDistY) {
        sideDistX = sideDistX + deltaDistX;
        mapX = mapX + stepX;
        side = 0;
      } else {
        sideDistY = sideDistY + deltaDistY;
        mapY = mapY + stepY;
        side = 1;
      }
      if (Array.GetItemCount(worldMap) < mapX) break;
      match(worldMap[mapX][mapY]) {
        9 => {
          if (mapX = posX) perpWallDist = 1; 
          else if (side = 0) perpWallDist = (mapX - posX + (1 - stepX) / 2) / rayDirX;
          else perpWallDist = (mapY - posY + (1 - stepY) / 2) / rayDirY;
          if (enemys[mapX +"x" + mapY] = "") storeenemydata();
          else enemys[mapX +"x" + mapY]["end"] = x;
        };
        8 => {
          if (mapX = posX) perpWallDist = 1;
          else if (side = 0) perpWallDist = (mapX - posX + (1 - stepX) / 2) / rayDirX;
          else perpWallDist = (posX + (1 - stepY) / 2) / rayDirY;
          if (bullets[mapX +"x" + mapY] = "") storebulletdata();
          else bullets[mapX +"x" + mapY]["end"] = x;
        };
      }
    } 
    if (Array.GetItemCount(worldMap) > mapX-1) {
      wall = storewalldata();
      match(worldMap[mapX][mapY]) {
        1 => wall["color"] = "#f00";
        //Match more coolors for different wall types
        _ => wall["color"] = "ff0";
      }
      Stack.PushValue("walls", wall);
    }
  }
}
function UpdateFrame() {
  BrushColor = "#333";
  FillRectangle(0, 0, width, height);
  while (Stack.GetCount("walls") <> 0) {
    wall = Stack.PopValue("walls");
    BrushColor = wall["color"];
    FillRectangle(wall["x1"], wall["y1"], 11, wall["y2"]-wall["y1"]);
  }
  for (enemy in enemys) {
    enmyX = enemy["start"];
    enmyY =  enemy["drawStart"];
    enmyWidth = enemy["end"] - enemy["start"];
    enmyHeight = enemy["drawEnd"]-enemy["drawStart"];
    DrawResizedImage(texture, enmyX, enmyY, enmyWidth, enmyHeight);
    BrushColor = "#f00";
    FillRectangle(enmyX+enmyWidth*0.2, enmyY, ((enmyWidth)-enmyWidth*0.4), enmyHeight*0.1);
    BrushColor = "#0f0";
    FillRectangle(enmyX+enmyWidth*0.2, enmyY, ((enmyWidth)-enmyWidth*0.4)/100*enmy[key]["Health"], enmyHeight*0.1);
    if (Math.GetRandomNumber(50) == 50) health--;
  }
  BrushColor = "#f39";
  for (bullet in bullets) {
    bulletX = bullet["start"];
    bulletY = bullet["drawStart"];
    bulletWidth = bullet["end"] - bullet["start"];
    bulletHeight = bullet["drawEnd"]-bullet["drawStart"];
    FillEllipse(bulletX+bulletWidth/2,bulletY+bulletHeight/2, bulletWidth*0.1, bulletWidth*0.1);
  }
  if (shoot <> 0) shoot--;
  DrawResizedImage(texture_gun[shoot], width/2-256/2, height-256,256,256);
  BrushColor = "#111";
  FillRectangle(0, height, width, scoreboardHeight);
  FontSize = scoreboardHeight-20;
  BrushColor = "#fff";
  DrawBoundText(10,height+10,width/3-20,"Enemys:" + Array.GetItemCount(enmy));
  BrushColor = "#f00";
  FillRectangle(width/3+10, height+10, width/3-20, 20);
  BrushColor = "#0f0";
  FillRectangle(width/3+10, height+10, (width/3-20)/100*health, 20);
  BrushColor = "#fff";
  DrawBoundText(width/3*2+10,height+10,width/3-20,"MS Elapsed: " + time);
}
//Threading
function SoundEngine () {
  Timer.Pause(); //stop the timer from reentering the function
  //loop this allows us to wait for the music to end and detect when it ends allowing consistent music throughout the game no matter how long you play for
  while (true) {
    Sound.Play(Program.Directory + "/Background.mp3");
    if (soundShoot) {
      soundShoot = false;
      Sound.PlayAndWait(Program.Directory + "/Shoot.wav");
    }
    if (PlayerInjured) {
      PlayerInjured = "False"
      Sound.PlayAndWait(Program.Directory + "/Injured.mp3");
    }
    Program.Delay(10);
  }
}
//events
function OnKeyDown() { 
  keys[LastKey] = true;
}
function OnKeyUp() {
  changed_key = GraphicsWindow.LastKey;
  if (changed_key == "Escape") { 
    MainMenuCleanup();
    InstructionsMenuCleanup();
    location = "MainMenu";
    Update = true;
  }
  if (changed_key == "Space") mouseDown = true;
  keys[changed_key] = false;
}
function OnControlClick() {
  match(Controls.LastClickedButton) {
    buttons["Play"] => {
      MainMenuCleanup();
      location = "Game";
      Update = true;
    };
    buttons["Map Maker"] => {
      MainMenuCleanup();
      location = "MapEditor";
      Update = true;
    };
    buttons["Instructions"] => {
      MainMenuCleanup();
      location = "Instructions";
      Update = true;
    };
    buttons["InstructionsBack"] => {
      InstructionsMenuCleanup();
      location = "MainMenu";
      Update = true;
    }
  }
}
//Init
init();
end;
```