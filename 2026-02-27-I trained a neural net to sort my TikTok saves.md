# I trained a neural net to sort my TikTok saves

I built a classifier that suggests a folder for each of my saved TikTok videos. The problem was a small piece of interface friction: saving a video, opening the folders, and selecting one required three separate taps. I often skipped the last two and ended up with an unsorted collection. A useful prediction could turn that into confirming a suggested folder.

The categories are personal. A model has to learn the way I organize videos, rather than assign them to a universal taxonomy. I wanted to see whether visual content and speech contained enough consistent signal to learn those choices from a small labeled set.

## Extracting the representation

I used pretrained models to extract features and trained a small classifier on top. For each video, I sample 5 frames uniformly and encode them with CLIP ViT-B/32. Each frame produces a 512-dimensional embedding; averaging them gives one visual vector for the video.

For the other modality, I extract the audio, transcribe it with Whisper, and encode the transcript with CLIP's text encoder. That produces another 512-dimensional vector. It represents the spoken content through the transcript, rather than directly modeling music or other audio signals. Speech was useful for distinguishing several of my folders.

I L2-normalize each modality independently and concatenate the vectors into a 1024-dimensional input. The classifier is an MLP with hidden widths of 256 and 128, followed by an output for each class. Independent normalization keeps one modality's vector magnitude from dominating before the classifier learns how to use them.

I trained with class-weighted cross-entropy because the folders were imbalanced. Without weighting, the model tended to predict the majority class and ignore smaller categories. Weighting made errors on those categories contribute more to training. The classifier trains in seconds; the expensive step is extracting CLIP and Whisper features, which took about 10 minutes for 600 videos on a consumer GPU.

## What the data supported

When TikTok was at risk of being banned, I saved around 600 bookmarked videos to a USB drive. The videos already in folders supplied labels. The remaining videos were the collection I wanted to sort.

The first version used 128 labeled videos across 6 categories and reached 93.8% cross-validation accuracy. After labeling more videos through the interface, I expanded to 213 labeled videos across 8 categories, with reported accuracy around 90%. These are results on my small personal dataset. They do not establish that the same setup would learn another person's folder choices equally well.

Recall varied by category. Quran recitation videos had a recognizable combination of visual framing and Arabic speech and reached near-perfect recall. Categories based on a more subjective reason for saving a video were harder, especially with fewer examples. The content can only explain a folder choice if the reason for that choice is represented in the features.

## Closing the labeling loop

I built a local web interface around a full-screen video player. Folder buttons sit on the left, and the model's top-3 predictions and scores appear on the right. The suggested folder is highlighted, so I can inspect the prediction while watching the video.

Clicking a folder or pressing a shortcut from 1–8 moves the file and advances to the next video. A retrain button runs feature extraction, training, and prediction generation in the background, then refreshes the suggestions. That makes the collection itself the source of additional labels as I correct or confirm the model.

The workflow is useful because it leaves the classification decision easy to check. Most videos take a few seconds to sort, and a correct suggestion reduces the action to confirmation. I still choose the folder when the prediction is wrong or the category depends on context the model cannot see.

## Possible extensions

I want to try prioritizing uncertain videos in the labeling interface. That could make each new label more informative, although uncertainty alone does not guarantee that an example will improve the model. I also want to test CLIP-based zero-shot suggestions for a new folder from its name, followed by ordinary supervised training as labels accumulate.

A browser extension could eventually suggest the folder at save time. The current implementation is local: Python, CLIP, Whisper, PyTorch, and FastAPI, with one HTML file containing the frontend's CSS and JavaScript. It is about 900 lines across 4 files and has no build step.
