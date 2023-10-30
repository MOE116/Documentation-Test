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
