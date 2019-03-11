SEMI-DEPRECATED
-------------
Exoplayer will soon have native support for ICY, see https://github.com/google/ExoPlayer/issues/3735. So that makes this solution almost obsolete. What does not exist in native Exoplayer yet is support for OGG metadata. (What I know of) 

Gradle
------
implementation 'com.github.materka:exoplayer-shoutcast-datasource:1.0.0'

Usage
------
```java
class MainActivity : AppCompatActivity(), ShoutcastMetadataListener {
    override fun onMetadataReceived(data: ShoutcastMetadata) {
        binding.metadata = MetadataBinder(
                data.getString(ShoutcastMetadata.METADATA_KEY_ARTIST),
                data.getString(ShoutcastMetadata.METADATA_KEY_TITLE),
                data.getString(ShoutcastMetadata.METADATA_KEY_SHOW),
                data.getLong(ShoutcastMetadata.METADATA_KEY_BITRATE).toString(),
                data.getString(ShoutcastMetadata.METADATA_KEY_GENRE),
                data.getLong(ShoutcastMetadata.METADATA_KEY_CHANNELS).toString(),
                data.getString(ShoutcastMetadata.METADATA_KEY_STATION),
                data.getString(ShoutcastMetadata.METADATA_KEY_URL),
                data.getString(ShoutcastMetadata.METADATA_KEY_FORMAT))
    }

    private val player by lazy {
        ExoPlayerFactory.newSimpleInstance(applicationContext,
                DefaultTrackSelector())
    }

    private var audioSource: ExtractorMediaSource? = null

    private val dataSourceFactory: ShoutcastDataSourceFactory by lazy {
        ShoutcastDataSourceFactory(
                Util.getUserAgent(applicationContext, getString(R.string.app_name)),
                this)
    }

    private lateinit var binding: ActivityMainBinding

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        binding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        btnMP3.setOnClickListener { play("http://http-live.sr.se/p3-mp3-192") }

        btnAAC.setOnClickListener { play("http://radio.canstream.co.uk:8075/live.aac") }

        btnOGG.setOnClickListener { play("http://revolutionradio.ru/live.ogg") }

        btnStop.setOnClickListener { stop() }
    }

    private fun play(url: String) {
        binding.metadata = null
        // String with the url of the radio you want to play
        val uri = Uri.parse(url)

        // This is the MediaSource representing the media to be played.
        audioSource = ExtractorMediaSource(uri,
                dataSourceFactory, DefaultExtractorsFactory(), null, null)
        player.prepare(audioSource)
        player.playWhenReady = true
    }

    private fun stop() {
        player.stop()
        binding.metadata = null
    }

    override fun onDestroy() {
        player.release()
        super.onDestroy()
    }
}
```

Supported Formats
------
* MP3
* AAC
* AAC+
* OGG
