---
layout: post
title: "Automated Boogle Solver"
metadescription: "How I automated the playing of Boogle with python, apis, image maninpulation and optical character recognition"
comments: true
---

Welcome back to my biennial blog post, it’s better later than never, patience makes the heart grow fonder or whichever excuse you’ll accept for my “lax” approach to blog writing. What have I been up to? Don’t let me keep you in suspense any longer as I have in store for you a walkthrough of how I automated the playing of the world-renowned, fast-paced, extreme (mental) sport of Boggle. For those unfamiliar with Boogle it is a game where you have a 4 X 4 grid of letters, Exhibit A, and a given time limit with the objective of finding as many words in sequences of adjacent letters.

![boogle_img](\images\example_grid.png){: .u-responsive-img style="margin: 0 auto;"}
Exhibit A

One day I was invited by a friend to engage with them in a friendly game of Word Blitz, a Boogle like game on Facebook. After quite a few games, and being the programmer that I am, definitely not because I am a sore loser… I started to wonder how difficult would it be to write a program to play the game on my behalf.

Being an online game, to register the words I have found it’s not as simple as typing them into a box I need to press the mouse down on the first letter and drag the mouse onto the second letter and so forth until the last letter and let the mouse button up. If I am to automate this process I need to create a program that can also control the mouse so I chose to write the program using the Python programming language utilising the  [PyAutoGUI](https://pyautogui.readthedocs.io/en/latest/){: .white .white-hover-dots target="_blank" } library to control the mouse.

There are 4 functions that the program is going to need to meet my needs:

1. Get a list of all the words in the given grid of letters
2. For each letter in the grid find the x and y coordinate on the screen
3. For each word find the path through the letters and converting it to a list of x,y coordinates
4. Have the mouse follow the coordinates for each word


## Step 1: Getting a list of words
{: .dotted .white-dots}

Given that I don’t see the grid of characters until a game has started I can’t spend half an hour looking for words before I start so I find a website that has a Boogle solver API, basically a website where I can send it the letters and it will return to me a list of all the words. I take maybe 10 seconds to manually transcribe the letters from the grid, send them off to the API and get my list of words, but a small price to pay in exchange for hundreds of words.

## Step 2: Locating my ABCs
{: .dotted .white-dots}

Fortunately for me, the game board is always located in the same spot on the screen with equally sized tiles for the letters, from this I can get all the information I need. I take a screenshot and open it in image editing software, what I’m looking for is the x,y position of the top left corner of the first letter tile in the grid and the width and height of the tile, this information is easily found and stored in the program. Now for the maths, yay, to make things easy for the future each tile/letter, I will these words interchangeably, is assigned a number from 0 - 15 (Exhibit B), the same as the indexing of an array, we’ll see why this is done later. Using the corner x,y position of the first letter and then adding half the width of the tile to the x position and half the height of the tile to the y position I have the centre of the first tile, to work out the centre of the rest of the tiles I simply add the width of the tile plus the gap between each tile to the centre of the first tile and do it 15 times, the 3 in the first row of letters and the 4 in rows 2-4. The newly acquired centres are stored in an array tileCentres so if I want to know where the centre is for tile 0 aka letter U, I just use tileCentres[0], remembering that the indexes of an array start at 0, so I have tileCenters 0-15.

![boogle_step_2](\images\example_step_2.png){: .u-responsive-img style="margin: 0 auto;"}
Exhibit B  

## Step 3: Finding my way
{: .dotted .white-dots}

To make it easy to find the location of letters on the grid I create a list of the letters and their position in the grid, so using the Exhibit B I have:

A: 11  
B: 10  
E: 2  
G: 9  
N: 4  
O: 8  
P: 5  
R: 7  
S: 13,14,15   
T: 3,12  
U: 0,6  
Y: 1  

I’ll start with an example using the word PET. To find the path I get the locations of the first letter, in this case it’s only 5, next I get the locations of the next letter E which is again only in position 2, I check if position 2 is one of the positions surrounding the previous position 5, 2 is a surrounding 5 so I can move on to the next letter T. T is found in positions 3 and 12, I will check them in order until I find one that is surrounding the E position of 2, 3 is surrounding 2 so I don’t need to check the next T position of 12. I check if there are any more letters and as it is the end of the word I know the tiles to make my word are 5,2,3. Knowing the tiles that make up the word I then use the tileCenter array to get the x,y positions for the tiles, i.e. tileCentres[5], tileCentres[2], tileCentres[3] and these are stored in another list for word paths. This process is repeated for each word.


## Step 4: Following the yellow brick road
{: .dotted .white-dots}

Having a list of all the x,y positions I need to visit with my mouse I use pyautogui to perform the mouse actions.

I take one list of x,y positions from the word paths at a time and I instruct pyautogui to move the mouse to the first x,y in the list and press down on the mouse, next it is instructed to drag the mouse to the next x,y position until it gets to the last position and then it releases the mouse.

## Step 1 Revisited
{: .dotted .white-dots}

Now the program is working but there is still one point that is bothering me, the manually transcribing the letters from the grid, surely there must be a way to automatically get them and pass them to the website and same me a precious 10 seconds, which in the end could equal hundreds of additional points.

The letters are clearly visible on the screen so it must be possible to use some sort of optical character recognition software to work out the individual letters. There is a tool that perfectly met my needs, [Tesseract](https://github.com/tesseract-ocr/tesseract){: .white .white-hover-dots target="_blank" } an Open Source OCR Engine, you can give it an image and it will find the words in it. 

To get screen grabs I use [Pillow](https://pypi.org/project/Pillow/){: .white .white-hover-dots target="_blank" } , a Python Imaging Library, I start by giving it the whole grid, Exhibit A, but it doesn’t like that, it has the random 2l, 3l, 2w badges on the tiles which throw things off and having the big gaps being letters didn’t help. 

Next, I try sending each individual letter through tesseract, knowing the centres of the tiles I can create a grid around the centre about 40 X 45 pixels and create a small image of that area of the screen, red boxes as shown in Exhibit C, it works better but it has trouble with certain characters like I, J, L and Q, O and a few others.

![step_one_revised](\images\example_step_1R.png){: .u-responsive-img style="margin: 0 auto;"}
Exhibit C  

I need better accuracy if this is going to be worth using, so instead of doing one letter at a time I try combining them into one image, Exhibit D, so it more closely resembles a word, though one that makes no sense, tada, it worked, mostly, still the occasional error with I, J, L so it’s not perfect but the errors are rare enough that I’m happy with the results.

![word_image](\images\word_image.png){: .u-responsive-img style="margin: 0 auto;"}
Exhibit D  

## Boggle solver in action
{: .dotted .white-dots}

After all this reading it is time to see it in action, in the video, you can see the letters from the grid combined into the singular image on the right which is then sent through tesseract and on the left you witness the game being played by my the program.

<div class="video-container">
<iframe src="https://www.youtube.com/embed/LVwZ3Lwdr9o" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen class="video"></iframe>
</div>


{% if page.comments %}
<div id="disqus_thread"></div>
<script>
    
    var disqus_config = function () {
    this.page.url = 'https://aaronhatchard.com.au{{ page.url}}'; 
    this.page.identifier = '{{page.id}}';
    };
    
    (function() { // DON'T EDIT BELOW THIS LINE
    var d = document, s = d.createElement('script');
    s.src = 'https://aaronhatchard.disqus.com/embed.js';
    s.setAttribute('data-timestamp', +new Date());
    (d.head || d.body).appendChild(s);
    })();
</script>
<noscript>Please enable JavaScript to view the <a href="https://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
{% endif %}