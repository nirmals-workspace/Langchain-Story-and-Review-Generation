# Langchain Story and Review Generation

## Aim
Generate a story using any Large Language Model (LLM). Then devise a method through which the output of the first LLM, the story, can be fed as input to a second LLM, which in turn generates a review of the story as its output.

## Prerequisites
Before you begin, you will need to have a few tools and libraries installed on your machine:
  - Python 3.7 or higher.
  - Langchain.
  - OpenAI and Cohere API keys.

## Workflow
- Started with brainstorming ideas to implement the same. While we could have easily done it with some API calls from two different models, I preferred to leverage the power of Langchain to get consistent and reliable results in same tone with required no. of tokens.
- Installed and Imported neccessary dependencies. Used only Langchain and some in-built libraries. But tried with few more libraries which I haven't shown in my notebook.
- Created two model instances one each for story and review generation. Here I happened to struggle a bit to establish the ideal second model for review generation. I will explain what I have gone through and how I handled the situation below.

    - After using `OpenAI model` for `story generation` which is `highly reliable` model with comparitively high input/output token limit, I **couldn't find any equally reliable model** for consistent and reliable output delivery **for review generation**.
    
    - I have gone through most important models available in HuggingFaceHub model repositories and tried my hands over many models using inference API of HuggingFaceHub.
    
    - Got access in a very short span of time in meta waitlist and tried the latest `Llama2-70b` model but it isn't providing API access as of now.
    
    - Then tried with `google-fang` models in HuggingFaceHub model space. Though they were accessible easily, they didn't provide output for a set no. of tokens.
    
    - Then searched whether there's access for Google's `BARD API` and Google's `PALM` model. But BARD doesn't provide API services as of now and PALM API available only in US and that too with quite a big waitlist to get access... So, left this option as well.
    
    - Then randomly I went through an old twitter(now X) DM from a developer where he seem to have asked me to test his new library named `litellm` that he built. But, I haven't noticed it back then and now went through his repo and found that he used `Cohere` somewhere. That's when I tried my hands with the same, got API access and have luckily found it to be `integrated with Langchain` as well.
        
        Also **important thing to notice** here is **even with cohere's nightly models, I couldn't get reliable results** for review generation though it was way better than other models that I have tried. Then I have gone through cohere website completely and found a way to build a **custom model with cohere as base model.** Then I `created custom jsonl dataset` with some `40 examples` of how the prompt would be and how the response should be using GPT 3.5 Turbo and used the same to fine-tune the custom model. That's what I have used as cohere model. You can see that I have given a lengthy string for model parameter - `'a6cbb8d1-1bb6-415c-9543-c0484ee3a5b8-ft'`.
        
        You can find a part of custom dataset below...
        
          {
          "prompt": "In a charming village nestled between rolling hills, lived a curious young fox named Finn. With fur as orange as autumn leaves, Finn had an insatiable appetite for knowledge and a heart full of bravery. One day, while exploring the outskirts of the village, Finn stumbled upon an ancient map tucked away in a forgotten chest in the hollow of an oak tree. The map seemed to depict a hidden cave at the heart of Whispering Woods, a place whispered about in local legends.\n\nIntrigued by the possibility of adventure, Finn embarked on a journey to uncover the secrets of Whispering Woods. Guided by the map's cryptic clues, he traversed babbling brooks, climbed rocky cliffs, and ventured deep into the heart of the forest. As the trees seemed to murmur with an almost mystical energy, Finn felt a mixture of excitement and trepidation.\n\nAfter days of tireless exploration, Finn stumbled upon a breathtaking sight—the entrance to a luminous cave adorned with glowing crystals of every hue. Inside, he discovered an intricate puzzle that seemed to guard a fabled treasure. Determined to prove his mettle, Finn spent hours solving the enigmatic puzzle, each piece unraveling a new mystery.\n\nWith the final piece set into place, the cave illuminated with a dazzling light, revealing not just a treasure, but a slumbering ancient creature. The creature, a guardian of wisdom, stretched and regarded Finn with eyes as old as time.\n\n“You have unlocked not only a treasure, but also the accumulated knowledge of generations,” the creature's voice resonated in Finn's mind. Grateful for being awakened from its long rest, the guardian shared tales of the world's history, the magic of nature, and the importance of courage.\n\nAs Finn listened to the guardian's words, he felt a deep connection to the past and a profound sense of responsibility for the future. With the newfound wisdom etched into his heart, Finn bid farewell to the guardian and embarked on a journey back home.\n\nWord of Finn's journey spread throughout the village, and the once ordinary young fox became a beloved figure—a symbol of the remarkable transformations that occur when one dares to venture into the unknown and emerges not only with treasure, but with wisdom to share.\n\nNow write a review of this story in {3} sentences without any bias",
          "completion": "The tale of Finn, the brave young fox, is an enchanting and thought-provoking adventure that emphasizes the value of curiosity, determination, and the pursuit of wisdom. Through vivid descriptions and engaging prose, readers are drawn into Finn's journey of discovery and growth. This captivating story not only entertains but also imparts a timeless lesson about the rewards that await those who embrace the mysteries of life."
          }
          {
          "prompt": "Deep within the cosmos, on a planet of shimmering blue lakes and iridescent trees, lived Lumina, a young stargazer with a heart full of dreams. Each night, Lumina would venture to the highest hill, her telescope pointed towards the infinite expanse above. One fateful night, as a meteor shower painted the sky with streaks of light, Lumina noticed a pattern—a constellation that seemed to pulse in a rhythmic dance.\n\nIntrigued and excited, Lumina embarked on a quest to decode the celestial dance. Guided by ancient scrolls and whispers from the wind, she journeyed to distant lands, collecting wisdom from scholars and seeking insights from mystics. Her journey took her across deserts of endless sands, through forests of whispering trees, and even to the edge of a starlit waterfall.\n\nAfter years of tireless exploration, Lumina pieced together the constellation's secrets. It was a map—a map that revealed the location of the Luminis Crystal, a gem said to hold the power of a thousand stars. With newfound determination, Lumina braved treacherous terrain and unraveled puzzles guarding the crystal.\n\nFinally, standing at the heart of the Celestial Cavern, Lumina found the Luminis Crystal, its luminous glow casting dancing reflections on the walls. As she touched the crystal, a surge of energy coursed through her, connecting her to the cosmos in ways she'd never imagined.\n\nWith the crystal's power, Lumina could now shape constellations, creating stories in the night sky for all to see. She etched tales of courage, love, and adventure, inspiring generations with her celestial artistry. Lumina's name echoed among the stars, a beacon of creativity and wonder.\n\nNow write a review of this story in {4} sentences without any bias",
          "completion": "Lumina's cosmic adventure is a mesmerizing blend of imagination, discovery, and the enchantment of the universe. The narrative weaves seamlessly between worlds, capturing both the vastness of the cosmos and the depths of Lumina's journey. With vivid prose and a touch of magic, readers are transported into a world where dreams and stars converge, leaving an indelible impression of the beauty that lies beyond our skies."
          }
          {
          "prompt": "In a peaceful village surrounded by lush meadows, there dwelled Mia, a young herbalist with a heart attuned to the rhythms of nature. Mia's days were spent tending to her garden, cultivating vibrant blooms and healing herbs that flourished under her care. One morning, as Mia wandered into the woods to gather rare ingredients, she stumbled upon an ancient stone tablet adorned with intricate symbols.\n\nIntrigued, Mia deciphered the symbols, revealing a forgotten recipe said to create a potion that could mend even the deepest wounds. Determined to unlock its power, Mia embarked on a quest, traveling across misty mountains and through enchanted forests to collect the rare ingredients.\n\nThrough her journey, Mia encountered creatures both mythical and mundane, each offering a lesson or a clue. Her compassion and respect for nature's balance earned her the trust of the forest's guardians, who guided her towards her goal. With the ingredients finally in her possession, Mia returned to her village.\n\nIn her garden, under the light of a full moon, Mia brewed the potion. Its ethereal glow reflected the beauty of her intentions and the wisdom she had gained. The potion was a success, but Mia realized that its true magic lay not only in its mending properties, but also in the unity it inspired.\n\nMia shared the potion's recipe with her village, encouraging them to work together, heal old wounds, and nurture their connections with one another. The village transformed, becoming a haven of compassion and camaraderie, where friendships flourished as vibrantly as Mia's garden.\n\nNow write a review of this story in {4} sentences without any bias",
          "completion": "Mia's journey as a herbalist weaves a tapestry of nature's wonders, ancient wisdom, and the transformative power of community. The story elegantly encapsulates Mia's growth as she connects with the environment, overcomes challenges, and spreads the message of unity. Through vivid prose and a profound understanding of human nature, readers are reminded of the healing potential that resides in both the natural world and the bonds we share."
          }

- Then I have used a nuanced way in Langchain to prompt the first LLM for story generation and got reliable and consistent result whenever I input some genre of my choice.
- Then in the review generation part, the prompt template might look so simple. But, that was made possible only because of the custom model that we built using Cohere.
- Finally, I structurised the output and put it together as a function and called it to get perfect result from genre of our choice.

## Sample Story and Review

<br>

**Story:**

Once upon a time, in a land far, far away, there was a little girl named Lily. Lily lived in a small village surrounded by thick, enchanted forests. This village was full of ordinary people, but Lily was no ordinary girl. She had a magical gift - the ability to talk to animals.

One day, while exploring the woods, Lily stumbled upon a wounded baby dragon. The dragon had a twisted wing and was unable to fly. Lily's heart filled with compassion, and she decided to help the dragon. She gently approached the dragon, speaking softly and reassuringly.

To her surprise, the dragon understood her words and responded with a low, rumbling purr. Lily carefully tended to the dragon's wing, placing a splint and bandages to help it heal. Days turned into weeks, and Lily visited the dragon every day, bringing it food and keeping it company.

As the dragon's wing began to heal, Lily realized that she needed to find a way to teach the dragon to fly. She consulted the wise old owl who lived in the tallest tree in the forest. The owl shared ancient wisdom and guided Lily on a quest to find a rare, magical feather that could grant the dragon the ability to fly once again.

Together, Lily and the dragon embarked on an incredible adventure. They traversed dark caves, crossed treacherous rivers, and climbed towering mountains. Along the way, they encountered talking animals, mischievous fairies, and kind-hearted giants who all joined their quest.

Finally, after many trials and tribulations, Lily found the magical feather hidden atop a majestic waterfall. She carefully plucked the feather and gently placed it on the dragon's back. As soon as the feather touched the dragon's scales, it began to shimmer and glow.

With a gleeful roar, the dragon spread its wings and soared into the sky, leaving streaks of sparkling fairy dust in its wake. Lily watched in awe as her beloved dragon friend soared higher and higher, exploring the vast, magical realm they had discovered together.

From that day forward, the dragon and Lily remained inseparable. Together, they embarked on countless adventures, helping others in need and spreading love and kindness throughout the enchanted land.

And as the village's resident dragon rider, Lily became a hero, inspiring others to embrace their own unique gifts and find the magic within themselves.

As the stars glittered in the night sky, Lily and her dragon friend would embrace each other, knowing that they were truly meant for each other, bound by a friendship that would last forever.

And with that heartwarming thought, little Lily closed her eyes, feeling safe and loved, drifting off into the sweetest of dreams. The end. 

Sleep tight, my dear friend. See you in the morning!

<br>

**Review:**

 The story is a heartwarming tale of friendship, compassion, and the magic of the natural world. The narrative transports young readers to a land where animals can talk and where the bond between a dragon and its human friend is one of pure love and adventure. Through its captivating portrayal of the characters and their journey to find the magical feather, the story resonates as a reminder that the simplest acts of kindness can create a world of wonder and joy.

## Contact
If you have any questions, comments, or suggestions for the project, please feel free to contact me at [nirmal.works@outlook.com]
