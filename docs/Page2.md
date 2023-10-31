# Features
## In-game Background Music
To implement this feature, a music file was added to the resources folder and a class  `MusicSettings` was added to the code to control the music playback actions. 
``` cs title="MusicSettings Class"
    public static class MusicSettings
    {
        public static SoundPlayer bgMusicPlayer;
        private static bool isBackgroundMusicPlaying = false;

        static MusicSettings()
        {
            // Initialize the background music player
            bgMusicPlayer = new System.Media.SoundPlayer(Resources.ShootToThrill); // call the music file from Resource using the file name
        }
        public static void PlayBackgroundMusic()
        {
            if (bgMusicPlayer != null && !isBackgroundMusicPlaying)
            {
                bgMusicPlayer.PlayLooping(); // start and loop the music playback
                isBackgroundMusicPlaying = true;
            }
        }
        public static void StopBackgroundMusic()
        {
            if (bgMusicPlayer != null && isBackgroundMusicPlaying)
            {
                bgMusicPlayer.Stop(); // stop music playback
                isBackgroundMusicPlaying = false;
            }
        }
    }

```
The methods defined in the class are used to start the music when the gams strarts, stop the music when the final boss battle is initialised and restart the music after
### Code for music in FrmLevel 
``` cs  hl_lines="4" title="Start Background Music"
      private void FrmLevel_Load(object sender, EventArgs e) {
      const int PADDING = 7;
      const int NUM_WALLS = 13;
      MusicSettings.PlayBackgroundMusic(); // start in-game music
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
      MusicSettings.PlayBackgroundMusic();  //restart background music

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

### CODE
``` cs
private void button1_Click(object sender, EventArgs e)    //Exit application
{
    Application.Exit();
}

```