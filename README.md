## Long Stable Diffusion: Long-form text to images
e.g. story -> Stable Diffusion -> illustrations

Right now, Stable Diffusion can only take in a short prompt. What if you want to illustrate a full story? Cue Long Stable Diffusion, a pipeline of generative models to do just that with just a bash script!

### Come at me with an example?
Yep! We just published [Never Hire a Herd of Goats to Mow your Lawn](https://storiesby.ai/p/never-hire-a-herd-of-goats-to-mow), an AI-generated story illustrated by this repo.

![Goat illustrations](https://user-images.githubusercontent.com/2941408/188747682-a751e2be-554e-4d05-ac08-a557d04b221a.png)

### Steps
1. Start with long-form text that you want accompanying images for, e.g. a story to illustrate.
2. Ask GPT-3 for several illustration ideas for beginning, middle, end, via the OpenAI API.
3. "Translate" the ideas from English to "prompt-English", e.g. add suffixes like `trending on art station` for better results.
4. The "prompt-English" prompts are put through Stable Diffusion to generate the images.
5. All the images and prompts are dumped into a `.docx`, for easy copy-pasting.

### Purpose
I made this to automate my self, ie. prompt AI for illustrations to accompany AI-generated stories, for the [Stories by AI](https://storiesby.ai/) podcast. Come check us out! And please suggest ways to improve—comments and pull requests are always welcome :) 

This was also just a weekend hackathon project to reward myself for doing a lot of work the past couple of months, and for feeling guilty about not using my wonderful and beautiful Titan RTXs to their full potential.

## Run
This bash script runs what you need. It assumes 2 GPUs with 24GB memory each. See the note above, under Steps, to change this assumption for your compute needs. I had too much fun with multiprocessing and making it faster.

`bash run_longsd.sh three_little_pigs`

To run your own text, replace `three_little_pigs` with the name of your new `.txt` file, put in the `texts/` folder.

#### What you need before you run it like that
- Install the requirements
- Make sure you set your OpenAI API key, e.g. in terminal `export OPENAI_TOKEN=<your_token>`
- Then, put your favorite story or article in a `.txt` file in the `texts/` folder

Check out the files for the example text, **Three Little Pigs**.

![threelittlepigs](https://user-images.githubusercontent.com/2941408/188760072-9765b085-1763-466e-8944-d4b9ecbb755b.png)

### Files and folders
- `run_longsd.sh`: This is the main entry script into the program to parallelize across GPUs easily.
- `longsd.py`: Where most of the magic happens: getting image prompts from GPT-3, making images from those prompts (using stable diffusion, multithreading), saving all those and also dumping those images and prompts to a docx file. This is what `run_longsd.sh` calls.
- `sd.py`: Just runs stable diffusion if you want to use it by itself (I do). `longsd.py` calls it.
- `dump_docx.py`: Just dumps image prompts and images into a single docx for a particular text. Again, it's useful if you want to use it by itself on the saved images and prompts. I do, because I'm actually overwriting the file when multiprocessing and sometimes will just use this as a postprocessing step. Yes, you can join those and change that but I don't really care, since sometimes my GPUs misbehave and I'll need to rerun it anyways.

- `texts/`: Folder to put your texts in, as a `.txt` file.
- `image_prompts/`: Generated image prompts by GPT-3 based on your text.
- `images`: Generated images by Stable Diffusion based on GPT-3's image prompts.
- `docx/`: Microsoft Word document for a text with images and their prompts all in one.

- `clean_lexica.py`: Preprocessing step for Stable Diffusion prompts from Lexica - clean up the prompts and put them into a single file.
- `effective_prompts_fs.txt`: Effective "prompt-English" to use for few-shot translation from English GPT-3 prompts to prompt-English (1884 tokens).

#### Multi-processing Multi-GPU Note
Multi-processing is optimized for 2 Titan RTXs, with 24GB RAM each. Changing the number of GPUs to parallelize on is a simple edit in `run_longsd.sh`: just copy the first line and change CUDA_VISIBLE_DEVICES to the appropriate GPU id. 

Changing the number of processes for each GPU is an argument that can be passed in through `run_longsd.sh` as `-n <num_processes_per_gpu>` for each run. This is an int used in `longsd.py`. I've found that my GPUs can handle 3, but are happier with 2.

### Complete
- [x] Pipeline of asking GPT3 for image prompts
- [x] Image prompts to stable diffusion
- [x] Multiprocessing to max out a single GPU
- [x] GPU multiprocessing stable diffusion
- [x] Docx dump of images and image prompts
- [x] Translation layer between English prompt and "prompt English" (lexica)
- [x] Flesh out readme
- [x] Open source

### Todo
- [ ] Walkthrough video of code
- [ ] Translation from English to 'prompt English' can be improved with: finetuned model with several million data samples (instead of 36)
