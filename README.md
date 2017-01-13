# Wav Utility for Unity
WAV utility for recording and audio playback functions in Unity.

# Usage
* Use **ToAudioClip** method for loading wav file / bytes.
  - Loads .wav (PCM uncompressed) files at 8,16,24 and 32 bits and converts data to Unity's AudioClip.
  ```
  public AudioSource audioSource; // hook up with scene's AudioSource in Unity Editor inspector
  public void LoadWavFile (string filename)
	{
		string path = string.Format ("{0}/{1}", Application.persistentDataPath, filename);
		AudioClip audioClip = WavUtility.ToAudioClip (path);
		audioSource.clip = audioClip;
		audioSource.Play ();
	}
  ```
* Use **FromAudioClip** method for saving wav file / bytes.
  - Converts an AudioClip's float data into wav byte array at 16 bit.
  ```
  private AudioClip audioClip;
  public int recordTime = 2; // no. of seconds can be set in Unity Editor inspector
  private const int sampleRate = 16000; // sample rate for recording speech
  public void RecordAudio ()
	{
		if (Microphone.devices.Length == 0) {
			Debug.LogWarning ("No microphone found to record audio clip sample with.");
			return;
		}
		string mic = Microphone.devices [0];
		audioClip = Microphone.Start (mic, false, recordTime, sampleRate);
	}
  ```
  ```
  public string SaveWavFile ()
	{
		string filepath;
		byte[] bytes = WavUtility.FromAudioClip (audioClip, out filepath, true);
		return filepath;
	}
  ```

# Notes
By design the **ToAudioClip** method only supports loading wav files that are stored using Unity's Application data path (`Application.persistentDataPath` or `Application.dataPath`). In order to load bundled resources it would be better to use Unity's `Resources.Load("filename") typeof(AudioClip)` method.

If you need to load WAV files from a remote URL then use the `UnityWebRequest.GetAudioClip` method which uses the AudioClip download handler.
```
public void LoadAudio ()
{
  string url = "<WAV_FILE_URL>";
  StartCoroutine (LoadAudioURL (url));
}
public IEnumerator LoadAudioURL (string url)
{
  UnityWebRequest www = UnityWebRequest.GetAudioClip (url, AudioType.WAV);
  yield return www.Send ();
  if (www.isError) {
    Debug.LogWarning ("Audio error:" + www.error);
  } else {
    AudioClip audioClip = ((DownloadHandlerAudioClip)www.downloadHandler).audioClip;
    audioSource.clip = audioClip;
    audioSource.Play ();
  }
}
```

# Version history
* 1.0 beta 1
  - Unity AudioClip to 16 bit wav file / bytes converter.
  - 8, 16, 24 or 32 bit PCM (uncompressed) wav file / bytes to Unity AudioClip converter.

# Related info
Wav file formatting based on specs detailed in following articles:
- http://soundfile.sapp.org/doc/WaveFormat/
- https://blogs.msdn.microsoft.com/dawate/2009/06/23/intro-to-audio-programming-part-2-demystifying-the-wav-format/
- http://wavefilegem.com/how_wave_files_work.html

Share the <3 [@deadlyfingers](https://twitter.com/deadlyfingers)
