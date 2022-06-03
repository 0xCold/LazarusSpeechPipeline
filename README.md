This program automates and performs the steps presented in https://github.com/Appen/UHV-OTS-Speech to easily create high-quality speech datasets through a number of pretrained machine learning models.

- Requirements:
    - Poetry installed

- Setup:
    - The program parses the /input folder to create speaker datasets. 
    - Simply create a subfolder, named after the speaker, and include any audio files you want to be parsed.
    - Additionaly, you can include a file named sources.json with youtube videos to be scraped and downloaded (example in /input/JuiceWRLD)

- Running:
    - Once inputs are prepared, simply run the program with "poetry run python pipeline.py"
    - The pipeline will parse inputs, scrape audio sources, and run through each step of the pipeline as explained below
    - If some steps need to be excluded, they can be easily commented out in the main loop of pipeline.py

- Pipeline:
    - Pre-Processing:
        - Synthetic Detection:
            - This model is trained to specifically detect synthetic speech (TTS) that if included in the speech dataset, would cause undesied results. 
                - Sections of the audio recordings it determines to be synthetic will be spliced and discarded now, so we don't have to worry about this

        - Source Separation:
            - This model is trained to split an audio recording into two separate files, one containing only the speech audio channel, and one containing only the background audio channel
                - The background audio will be discarded, and the rest of the pipeline works on the isolated speech.
                - This is useful to remove background sound that would otherwise be included (wind blowing, highway sounds) but can also be used to isolate vocals from music, allowing for the creation of specific datasets for people singing, rapping, etc 

    - Pre-Tagging:
        - Speech Detection:
            - This model detects sections of speech in an audio clip (without transcribing it), and produces a file with timestamped sections of speaking, silence, and other noise.
                - All non-speech sections can be discarded, greatly decreasing pipeline runtime by only running the following models on actual speech

        - Speaker Segmentation:
            - This model attempts to differentiate between speakers in the audio file, assigning a speaker label to each transcribed audio file
                - Audio files from other speakers can then be discarded (likely one of the big problems in prev. pipeline)

        - Speech Transcription:
            - This model transcribes the audio files from the desired speaker, producing a file with timestamped captions for each audio file

        - Topic Detection:
            - This model parses the transcribed audio files and assigns a topic label to each one
                - This has some interesting possibilities, could use it to create specific models for when speakers are talking about sad stuff, are angry, etc. Could be cool but maybe not necessary
