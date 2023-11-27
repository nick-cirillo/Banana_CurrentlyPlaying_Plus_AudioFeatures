# Banana_CurrentlyPlaying_Plus_AudioFeatures

An extension of the Banana_CurrentlyPlaying TouchDesigner Spotify API integration. Includes the ability to extract a track's "audio features," such as the energy, tempo, and valence, as provided by the Spotify API. 

The main additions to this integration include additional parameters for the audio features of tracks on Spotify, and a new API call that is only sent when the Track ID updates to prevent rate-limiting, as well as a lightly updated version of jetXS's original documentation. 

# Thank You to jetXS
A big thank you to jetXS, the original repository creator, for creating an excellent integration and clear instructions!

## How to Set Up
### Spotify Developer App
1. You will need first to register a Spotify Developer App, as you need your own Client ID and Client Secret.
2. Go to https://developer.spotify.com/, and log with your Spotify account.
3. Go to [Dashboard](https://developer.spotify.com/dashboard/applications).
4. Click on My New App / Create an App.
5. Once everything is filled, you will see a tile for that app in your Dashboard. Click on it to reveal the Client ID and Client Secret underneath your app description.
6. Save those keys, we will need them later.
7. Click on the top right, edit settings, and go to the Redirect URIs section, add something such as `http://localhost:XXXX`, where XXXX is some local port that you want to use to link TouchDesigner and the Spotify API together.
8. Save this link for later as well, including the port.

### Release TOX vs TOE

The original Banana_CurrentlyPlaying repository allowed you to use either a .tox or .toe file format. In this extension, only the .tox file format is provided. You can drag-and-drop this extension into your projects. **Make sure that if you modify this extension, you export it as a .tox separately, as your overall project may not save the changes.**

### Using the component for the first time (the TOX way)

When you open the .tox file for the first time (such as by dragging the file into a TouchDesigner project), an error message will appear. We'll dismiss that now by filling out the Client ID, Client Secret, and the port.

1. In TouchDesigner, fill the parameter of the component called `Redirect URI` with the `http://localhost:XXXX` link from earlier.
1. Go back to your Spotify App Dashboard and copy the Client ID.
3. Paste it on the TouchDesigner component, in the parameter `Client ID`.
4. Do the same for `Client Secret`. You may need to click "reveal Client Secret" on the Spotify dashboard.
5. Pulse the Force Re-Auth parameter.

Now, your browser should have a new tab that asks for authentication for your Spotify account. This is normal - Spotify is making sure that you would like to allow access to some of your personal information. The token that will be generated is scoped for this extension only.

Once you log in, you should see a message appear in your browser confirming that the link between TouchDesigner and Spotify's API was made. If nothing happens, you might want to pulse again Force Re-Auth. In the background, we are using the WebServer DAT from TouchDesigner. Sometimes it is a bit finicky on startup.

If you look at the TouchDesigner component, the fields `Token` and `Refresh Token` should be now filled with values.

You are now ready to use the component.

## How to access the exposed data

From anywhere in your TouchDesigner application, type `op.BANANA_CURRENTLYPLAYING.{Propertyname}`

The properties from the currently-playing API call are:
- Artists - A list of artists on the currently playing track - String - `op.BANANA_CURRENTLYPLAYING.Artists`
- Trackname - The title on the currently playing track - String - `op.BANANA_CURRENTLYPLAYING.Trackname`
- Albumcover - The URL to the image of the album cover of the currently playing track - String - `op.BANANA_CURRENTLYPLAYING.Albumcover`
- ID - The ID of the track internal to Spotify - String - `op.BANANA_CURRENTLYPLAYING.Id`

The properties from the audio-features API call are:
- Acousticness - A confidence measure from 0.0 to 1.0 of whether the track is acoustic. 1.0 represents high confidence the track is acoustic - float - `op.BANANA_CURRENTLYPLAYING.Acousticness`
- Danceability - Danceability describes how suitable a track is for dancing based on a combination of musical elements including tempo, rhythm stability, beat strength, and overall regularity. A value of 0.0 is least danceable and 1.0 is most danceable - float - `op.BANANA_CURRENTLYPLAYING.Danceability`
- Energy - Energy is a measure from 0.0 to 1.0 and represents a perceptual measure of intensity and activity. Typically, energetic tracks feel fast, loud, and noisy. For example, death metal has high energy, while a Bach prelude scores low on the scale. Perceptual features contributing to this attribute include dynamic range, perceived loudness, timbre, onset rate, and general entropy - float - `op.BANANA_CURRENTLYPLAYING.Energy`
- Instrumentalness - Predicts whether a track contains no vocals. "Ooh" and "aah" sounds are treated as instrumental in this context. Rap or spoken word tracks are clearly "vocal". The closer the instrumentalness value is to 1.0, the greater likelihood the track contains no vocal content. Values above 0.5 are intended to represent instrumental tracks, but confidence is higher as the value approaches 1.0 - float - `op.BANANA_CURRENTLYPLAYING.Instrumentalness`
- Liveness - Detects the presence of an audience in the recording. Higher liveness values represent an increased probability that the track was performed live. A value above 0.8 provides strong likelihood that the track is live - float - `op.BANANA_CURRENTLYPLAYING.Liveness`
- Loudness - The overall loudness of a track in decibels (dB). Loudness values are averaged across the entire track and are useful for comparing relative loudness of tracks. Loudness is the quality of a sound that is the primary psychological correlate of physical strength (amplitude). Values typically range between -60 and 0 db - float - `op.BANANA_CURRENTLYPLAYING.Loudness`
- Speechiness - Speechiness detects the presence of spoken words in a track. The more exclusively speech-like the recording (e.g. talk show, audio book, poetry), the closer to 1.0 the attribute value. Values above 0.66 describe tracks that are probably made entirely of spoken words. Values between 0.33 and 0.66 describe tracks that may contain both music and speech, either in sections or layered, including such cases as rap music. Values below 0.33 most likely represent music and other non-speech-like tracks - float - `op.BANANA_CURRENTLYPLAYING.Speechiness`
- Tempo - The overall estimated tempo of a track in beats per minute (BPM). In musical terminology, tempo is the speed or pace of a given piece and derives directly from the average beat duration - float - `op.BANANA_CURRENTLYPLAYING.Tempo`
- Timesig - An estimated time signature. The time signature (meter) is a notational convention to specify how many beats are in each bar (or measure). The time signature ranges from 3 to 7 indicating time signatures of "3/4", to "7/4" - int - `op.BANANA_CURRENTLYPLAYING.Timesig`
- Valence - A measure from 0.0 to 1.0 describing the musical positiveness conveyed by a track. Tracks with high valence sound more positive (e.g. happy, cheerful, euphoric), while tracks with low valence sound more negative (e.g. sad, depressed, angry) - float - `op.BANANA_CURRENTLYPLAYING.Valence`

Spotify provides more audio features than just these. It's fairly straightforward to add them - you will need to copy everything the other parameters do in CurrentlyPlayingExt.

As long as something is playing from any of your devices (not necessarily local stream, it can be from your phone or Sonos or whatever you like), then these parameters will be updated with a small delay. The delay can be reduced by lowering the `period` parameter in the `beat1` operator, but if you lower it too much, you will be rate-limited by Spotify, and you will not be able to retrieve audio features (though the currently_playing API call usually still works). A `period` of 8 is the default, but I've run it at 4 with no issues.

When you pause or close Spotify, the values of all track information and audio feature parameters are reset to empty strings.

# BANANA_
The Banana_ repos are community oriented repositories, they are under GNU GPL 3 license. Please feel free to report issues, do pull requests or RFE, it's a community effort.
