# Features
## In-game Background Music
To implement this feature, a music file was added to the resources folder and a class  `MusicSettings` was added to the code to control the music playback actions. 
``` cs title="MusicSettings Class"
     static MusicSettings()
 {
     // Initialize the background music player
     bgMusicPlayerMainMenu = new SoundPlayer(Resources.paintheme);
     bgMusicPlayerLevel = new SoundPlayer(Resources.ShootToThrill); // Replace with the actual resource name
 }
 public static void PlayBackgroundMusicMainMenu()
 {
     if (bgMusicPlayerMainMenu != null && !isBackgroundMusicPlaying)
     {
         bgMusicPlayerMainMenu.PlayLooping();
         isBackgroundMusicPlaying = true;
     }
 }
 public static void PlayBackgroundMusicLevel()
 {
     if (bgMusicPlayerLevel != null && !isBackgroundMusicPlaying)
     {
         bgMusicPlayerLevel.PlayLooping();
         isBackgroundMusicPlaying = true;
     }
 }
 public static void StopBackgroundMusic()
 {
     if (bgMusicPlayerMainMenu != null && isBackgroundMusicPlaying)
     {
         bgMusicPlayerMainMenu.Stop();
         isBackgroundMusicPlaying = false;
     }
     if (bgMusicPlayerLevel != null && isBackgroundMusicPlaying)
     {
         bgMusicPlayerLevel.Stop();
         isBackgroundMusicPlaying = false;
     }
 }
```
The methods defined in the class are used to start the music when the gams strarts, stop the music when the final boss battle is initialised and restart the music after
### Code for music in FrmLevel 
``` cs  hl_lines="5" title="Start Background Music"
      private void FrmLevel_Load(object sender, EventArgs e) {
const int PADDING = 7;
const int NUM_WALLS = 19;
  MusicSettings.StopBackgroundMusic();
  MusicSettings.PlayBackgroundMusicLevel();
```

### Code for music in FrmBattle
``` cs title="Stop Background Music"
    public void SetupForBossBattle() {
    
      picBossBattle.Location = Point.Empty;
      picBossBattle.Size = ClientSize;
      picBossBattle.Visible = true;

      MusicSettings.StopBackgroundMusic(); //stop background music
      SoundPlayer simpleSound = new SoundPlayer(Resources.final_battle);
      simpleSound.Play();// play intro audio for BossBattle
      
      tmrFinalBattle.Enabled = true;
    }
```

``` cs title="Restart Background Music"
    private void tmrFinalBattle_Tick(object sender, EventArgs e) {
      picBossBattle.Visible = false;
      tmrFinalBattle.Enabled = false;
      MusicSettings.PlayBackgroundMusicLevel();  //restart background music

    }

```
## Flee Ability
This feature was added to the FrmBattle.cs code. This feature allows the player to flee from a battle confrontation with an enemy by clickig a button on the battle screen. The button click event is controlled by a method `FleeButton_Click`.
The method closes the battle window in the event of a click on the "Flee" button.
``` cs title="FleeButton click event handler"
        private void FleeButton_Click(object sender, EventArgs e)
        {
            instance = null;
            Close();
        }

```
## Enemies disappear after health reaches 0.
To implement this feature changes were made to `FrmLevel.cs` Class inside the `method Fight()` where an event handler was added `frmBattle.FormClosed` which is triggered when frmBattle is closed and when player health is greater than zero and enemy health is less than 0 all enemy characters are hidden when this criteria is met. 
### Class FrmLevel.cs
``` cs
 frmBattle.FormClosed += (s, ev) =>  // Make Enemy disappear
         {
             if (player.Health > 0 && enemy.Health <= 0)
             {
                 if (enemy == bossKoolaid)
                 {
                     picBossKoolAid.Hide(); // Hides the bossKoolaid character
                 }
                 else if (enemy == enemyPoisonPacket)
                 {
                     picEnemyPoisonPacket.Hide(); // Hides the enemyPoisonPacket character
                 }
                 else if (enemy == enemyCheeto)
                 {
                     picEnemyCheeto.Hide(); // Hides the enemyCheeto character
                 }
             }

         };

```

## Character Heal
In this feature the player character is able to increase their healthcount by clicking on heal button which was created in `FrmBattle.Designer.cs` and `FrmBattle.cs`. 
### In FrmBattle.cs Class
``` cs 
private int healCount = 0;   //Heal count for heal button added
```
This integer variable keeps count of number of heals player has used.
``` cs
private void Healbtn_Click(object sender, EventArgs e)  //Player heal
{
    if (healCount < 5)
    {
        if (player.Health < player.MaxHealth)
        {
            player.AlterHealth(5);
            UpdateHealthBars();
            healCount++;

        }
    }
}
```
Created a method Called Healbtn_Click Which is associated with the click event of Heal button and checks if healcount is less then 5 is so then checks if player health is less than the maximum health and if true adds 5 health to the health bar and updates the healcount. 

## Game Start Tutorial
Created a new class tutorial form and called it to FrmLevel.cs to ensure the tutorial window pops up when game is started which shows up on top of the game screen and can be closed to begin playing the game. 
### Inside FrmLevel.cs
``` cs 
private FrmTutorial frmTutorial;
Game.player = player;
timeBegin = DateTime.Now;
frmTutorial = new FrmTutorial();           
frmTutorial.TopMost = true;
frmTutorial.Show();
```
### Code for FrmTutorialDesigner.cs
``` cs
    // 
    // FrmTutorial
    // 
    this.AutoScaleDimensions = new System.Drawing.SizeF(8F, 16F);
    this.AutoScaleMode = System.Windows.Forms.AutoScaleMode.Font;
    this.ClientSize = new System.Drawing.Size(800, 450);
    this.Controls.Add(this.textBox3);
    this.Controls.Add(this.pictureBox3);
    this.Controls.Add(this.pictureBox2);
    this.Controls.Add(this.textBox2);
    this.Controls.Add(this.textBox1);
    this.Controls.Add(this.pictureBox1);
    this.Name = "FrmTutorial";
    this.Text = "FrmTutorial";
    this.Load += new System.EventHandler(this.FrmTutorial_Load);
    ((System.ComponentModel.ISupportInitialize)(this.pictureBox1)).EndInit();
    ((System.ComponentModel.ISupportInitialize)(this.pictureBox2)).EndInit();
    ((System.ComponentModel.ISupportInitialize)(this.pictureBox3)).EndInit();
    this.ResumeLayout(false);
    this.PerformLayout();

}

```
## Low Health Pop-Up (Below 20%)
In this feature if the player health drops below a certain percent they can see a pop warning which shows that they have low health. This is done by making changes to FrmBattle.cs inside the method `UpdateHealthBars()` where it checks the player health and shows a messagebox.
``` cs
private void UpdateHealthBars()
 {
     float playerHealthPer = player.Health / (float)player.MaxHealth;
     float enemyHealthPer = enemy.Health / (float)enemy.MaxHealth;

     const int MAX_HEALTHBAR_WIDTH = 226;
     lblPlayerHealthFull.Width = (int)(MAX_HEALTHBAR_WIDTH * playerHealthPer);
     lblEnemyHealthFull.Width = (int)(MAX_HEALTHBAR_WIDTH * enemyHealthPer);

     lblPlayerHealthFull.Text = player.Health.ToString();
     lblEnemyHealthFull.Text = enemy.Health.ToString();

     if (playerHealthPer < 0.3)                                         // Health Warning
     {
         MessageBox.Show("Warning: Low health!");

     }

 }

```
## Pause Game
This feature was added to the FrmLevel.cs code. This feature allows the player to Pause and Play the Game, by clicking a button on the Game screen. The button click event is controlled by a method `button1_Click`. This method toggles the Game play in between play and pause, in the event of a click on the "Pause" button.

### Code
``` cs 

  private void button1_Click(object sender, EventArgs e)              //Pause button 
  {
      if (!isPaused)
      {
          tmrUpdateInGameTime.Stop();
          isBackgroundMusicPlaying = false;
          tmrPlayerMove.Stop();
          button1.Text = "PLAY";
      }
      else
      {
          tmrUpdateInGameTime.Start();
          isBackgroundMusicPlaying = true;
          tmrPlayerMove.Start();
          button1.Text = "PAUSE";
      }
      isPaused = !isPaused;
  }

```
Later I initialized the isPaused - a variable to understand the state of the game true or false.
### Code
``` cs 
    private bool isPaused = false;         // Pause button
```
As the pause button was having more focus to shift the focus from pause button to game I have written a code to methodoverride so the game gains the focus and to make player movement more ease.The method always returns true, indicating the key press has been handled and shouldn't be processed further by the system.
``` cs 
protected override bool ProcessCmdKey(ref Message msg, Keys keydata)     //Movement change
{
    player.ResetMoveSpeed();

    switch (keydata)
    {
        case Keys.Left: player.GoLeft(); break;
        case Keys.Right: player.GoRight(); break;
        case Keys.Up: player.GoUp(); break;
        case Keys.Down: player.GoDown(); break;
    }

    return true;
}

```
## Exit Button (to go out of the game)
This feature was added to the FrmBattle.cs code. This feature allows the player to Exit from a battle, by clicking a button on the battle screen. The button click event is controlled by a method `button1_Click`. The method closes the Game window in the event of a click on the "Exit" button.

### Code
``` cs
private void button1_Click(object sender, EventArgs e)    //Exit application
{
    Application.Exit();
}

```

## Ingame Adverts
This feature allows the developer to display advertisments to the user when they are playing the game.
To implement this feature, a panel was added to the FrmBattle.Designer.cs.
The function `Advisible` takes the color of the battle form background as an input to determine which advertisment image to display on the AD-panel
and which url to redirect to when clicked.
A method was designed to handle the panel click event as well, `advertisingPanel_Click`.
To close the advert, a textbox was added to the panel. The textbox is designed to cycle through the string values in a list.
At the end of the cycle the user can close the advert panel by clicking on the  box. The method `AdClose_Click` handles the click event to close 
the ad panel.

## Game-Story.
The Game story is designed to present the player with the story behind the game and provide context for Mr Peanut's circumstances and objectives.
The game story is implemented in a form, `FrmMainMenu`.
A text box was added to the `FrmMainMenu` designer to display the story in lines of text the continues to cycle through the entire story as long as the MainMenu is open.
The `scrolltimer` timer was added to the FrmMainMenu.designer to control the speed of the storyline cycle.

## Textured Walls and Floor
Added Wall.png to Resource.resx and data library and further changed it in FrmLevel.cs. included a background floor texture in FrmLevelDesigner.cs and Implemented new collision logic for the new wall texture. 
Inside FrmLevel.cs in FrmLevel_Load
``` cs title="code for new texture and walls"

            walls = new Character[NUM_WALLS];

            // Loop over control names that could represent walls
            string[] wallNames = new string[] {
    "picWall0", "picWall1", "picWall2", "picWall3", "picWall4", "picWall5",
    "picWall6", "picWall7", "picWall8", "picWall9", "picWall10", "picWall11",
    "picWall14", "pictureBox1", "pictureBox2", "pictureBox4", "picWall12", "pictureBox3"
};

            for (int w = 0, index = 0; w < wallNames.Length; w++) 
            {
                Control[] foundControls = Controls.Find(wallNames[w], true);
                if (foundControls.Length > 0)
                {
                    PictureBox pic = foundControls[0] as PictureBox;
                    walls[index++] = new Character(CreatePosition(pic), CreateCollider(pic, PADDING));
                }
                else
                {
                    // Log or handle the case where a PictureBox is not found
                    Debug.WriteLine("PictureBox not found: " + wallNames[w]);
                }
            }
```
## Items interactions and stats change
Created a new item in FrmLevel.cs Added a PictureBox of a Knife and made it be able to pick up and increase the player’s attack power by multifold. Made changes to FrmLevel.cs FrmDesigner.cs, FrmBattleCharacter. 
Inside FrmLevel.cs 
```cs 
private bool hasKnife = false;
  private void PickUpKnife()
  {
      hasKnife = true;
      picKnife.Visible = false; // Hides the knife from the game world
      player.AttackPower += 0.5f; // Increases attack power
      MessageBox.Show("You picked up the knife! Your attack power has increased.", "Knife Acquired", MessageBoxButtons.OK, MessageBoxIcon.Information);
  }

Inside BattleCharacter.cs 

public BattleCharacter(Vector2 initPos, Collider collider) : base(initPos, collider)
{
    MaxHealth = 20;
    strength = 2;
    Health = MaxHealth;
    AttackPower = 1; // Default attack power
}

public void OnAttack(int amount)
{
    AttackEvent((int)(amount * strength * AttackPower)); // Use AttackPower in calculation
}

```

![knife.png is the image](images/knife.png)

## Main Menu Page
Created a new Windows form `FrmMainMenu.cs` to implement the main menu and called it to program.cs to ensure that main menu window pops up when game starts. It displays a start button and has a tutorial button. Methods `StartBtn_Click` and `button1_Click` were created in `FrmMainMenu.cs` to handle the button_click event for both buttons respectively. 

![MainMenu.png is the image](images/MainMenu.png)

![Tutorial.png is the image](images/Tutorial.png)

## Game Over Screen
This feature was added to the `Frmbattle.cs` code. This feature lets the player know the game has come to completion, a new form that will be displayed when the game ends. This form  show message `Game Over` , along with option to restart the game.

![Gameover.png is the image](images/Gameover.png)

## Always Visible Player Health Status
	
This feature was added to the FrmLevel.cs code. This feature allows the player to monitor player health throughout the game, This feature enhances the gaming experience, adding to the game's strategy. A user interface element (PlayerHealth)  was added and updated it based on the player's health status.

![Health.png is the image](images/Health.png)



