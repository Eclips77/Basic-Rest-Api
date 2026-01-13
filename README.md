flowchart TD
    %% ===== ENV =====
    ENV[ENV CONFIG<br/>
    METADATA_STORAGE=mongo|json<br/>
    DATA_STORAGE=mongo<br/>
    FILE_STORAGE=fs]

    %% ===== BASE ABSTRACTIONS =====
    MetaBase[MetadataStorage<T>]
    DataBase[DataRepository<T>]
    EncodeBase[EncodingService]
    FileBase[FileStorage]

    %% ===== METADATA IMPLEMENTATIONS =====
    MetaMongo[MongoMetadataStorage<T><br/>+ model]
    MetaJson[JsonMetadataStorage<T><br/>+ filePath + type]

    MetaBase --> MetaMongo
    MetaBase --> MetaJson

    %% ===== DATA IMPLEMENTATION =====
    DataMongo[MongoDataRepository<T>]
    DataBase --> DataMongo

    %% ===== FILE STORAGE =====
    FS[FsFileStorage<br/>+ basePath]
    FileBase --> FS

    %% ===== ENCODING =====
    Encoder[VideoEncodingService<br/>FFmpeg]
    EncodeBase --> Encoder

    %% ===== SMART MODULES =====
    MetaModule[MetadataModule.forFeature()]
    DataModule[DataModule.forFeature()]
    FileModule[FileModule.forRoot()]
    EncodeModule[EncodingModule]

    ENV --> MetaModule
    ENV --> DataModule
    ENV --> FileModule

    %% ===== TOKENS =====
    META_TOKEN[(METADATA_STORAGE)]
    DATA_TOKEN[(DATA_REPOSITORY)]
    FILE_TOKEN[(FILE_STORAGE)]
    ENCODE_TOKEN[(ENCODER)]

    MetaModule -->|mongo| MetaMongo
    MetaModule -->|json| MetaJson
    DataModule --> DataMongo
    FileModule --> FS
    EncodeModule --> Encoder

    MetaMongo --> META_TOKEN
    MetaJson --> META_TOKEN
    DataMongo --> DATA_TOKEN
    FS --> FILE_TOKEN
    Encoder --> ENCODE_TOKEN

    %% ===== FEATURE MODULES =====
    VideoModule[VideoModule]
    GenreModule[GenreModule]
    PlaylistModule[PlaylistModule]

    VideoModule --> MetaModule
    VideoModule --> DataModule
    VideoModule --> FileModule
    VideoModule --> EncodeModule

    GenreModule --> MetaModule
    GenreModule --> DataModule

    PlaylistModule --> MetaModule
    PlaylistModule --> DataModule

    %% ===== SERVICES =====
    VideoService[VideoService]
    GenreService[GenreService]
    PlaylistService[PlaylistService]

    META_TOKEN --> VideoService
    DATA_TOKEN --> VideoService
    FILE_TOKEN --> VideoService
    ENCODE_TOKEN --> VideoService

    META_TOKEN --> GenreService
    DATA_TOKEN --> GenreService

    META_TOKEN --> PlaylistService
    DATA_TOKEN --> PlaylistService