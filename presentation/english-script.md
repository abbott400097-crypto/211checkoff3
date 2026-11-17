# Complete English Presentation Script

Read the spoken English; bracketed directions are presentation cues.

Nine slides; approximately 696 spoken words. Rehearse at a comfortable pace with the two audience pauses to stay within 5–7 minutes.

## Slide 1: Same pixels. Fewer bytes.

Hi, I’m Bingxi. Today I’m talking about a simple way to make some image data smaller, called run-length encoding, or R-L-E.

[Point to the heart.]

This heart is made of small colored squares called pixels. To store the picture, a computer needs to record the color of every pixel.

But many nearby pixels have the same color. RLE groups consecutive pixels of the same color and records how many there are. This can save space while keeping the picture exactly the same.

Let’s try it with one row.

## Slide 2: How would you describe this row?

Here are thirty-two pixels. A, B, C, and D are labels for four different colors.

For this example, each pixel’s color uses one byte, so this row takes thirty-two bytes.

[Point across the row.]

If you were describing this row to someone, how could you say it without naming every pixel separately?

[Wait 5–10 seconds for an answer. Then advance.]

Here is a shorter description.

## Slide 3: A row becomes four runs

Eight As, eight Bs, eight Cs, and eight Ds. That gives us four groups. In RLE, each group of consecutive equal values is called a run.

[Point to the first pair.]

This pair means “eight pixels of color A.” The first number tells us how many. The second part tells us the color.

For this demo, the count and the color each use one byte. So each pair takes two bytes, and four pairs take eight bytes.

We went from thirty-two bytes to eight, saving seventy-five percent. This comparison covers pixel data only. The image dimensions and the color definitions are already known.

## Slide 4: Decoding restores every pixel

Now let’s turn those pairs back into pixels. That process is called decoding.

[Point from the first pair to the row below.]

Eight and A tells us to write A eight times. We do the same for B, C, and D.

We get exactly the original values, in the original order. That is what lossless means.

The C program checks this by comparing the restored length and every restored byte with the original data.

## Slide 5: Will this row get smaller?

Now we have another row of thirty-two pixels. This time, the colors keep switching: A, B, A, B.

We still need two bytes for each count-and-color pair.

Do you think RLE will make this row smaller, keep it the same size, or make it bigger?

[Wait 5–10 seconds for an answer. Then advance.]

Let’s check.

## Slide 6: Compression can make data bigger

It gets bigger. Every time the color changes, a new run begins. Here, each run contains only one pixel.

[Point to the pairs.]

We have one A, one B, one A, and so on. Thirty-two pixels now need thirty-two pairs. At two bytes per pair, that is sixty-four bytes, twice the original size.

We can still recover every pixel correctly, so it is still lossless. But the extra count bytes make this particular input larger.

## Slide 7: ABAB still has a pattern

Look at the alternating row again. It still has a pattern: AB keeps repeating.

Our RLE counts consecutive copies of one value, like the As at the top.

[Point to the AB groups.]

A method called L-Z seventy-seven can use repeated sequences. It refers back to matching data that appeared earlier, so it can take advantage of this repeated AB pattern.

The exact space saving depends on how it stores those references. My demo implements RLE, not LZ77.

## Slide 8: Same size. Different patterns.

Here is how our RLE behaves on three pictures. They all have twelve by twelve pixels, or one hundred and forty-four bytes before encoding.

We read each row from left to right, continuing onto the next row. Our format allows a run to cross that boundary.

[Point left, then middle, then right.]

The left picture has two large blocks of color. That means two runs, or four bytes.

The heart has nineteen runs, counting both the heart and its background, so it takes thirty-eight bytes.

On the right, every next pixel changes color. That produces one hundred and forty-four pairs, or two hundred and eighty-eight bytes.

The same image dimensions can give very different results.

## Slide 9: RLE works best with long runs

The main lesson is that RLE works best with long stretches of the same value.

This also connects directly to C. The encoder walks through an array, compares neighboring values, counts a run, and writes a count-and-value pair into another array.

Our stored count is one unsigned byte, so the maximum is two hundred and fifty-five. If a run is longer, we split it into more pairs.

For these examples, compression can save space or make the data larger. We need to check the result for the actual input.

Thank you. What questions do you have?
