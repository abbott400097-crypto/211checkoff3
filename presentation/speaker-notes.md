# Speaker notes

Use the separate English script for the latest wording; the existing presentation's embedded notes still contain the earlier version.

## Slide 1: Same pixels. Fewer bytes.

中文：第一页先指爱心，把像素、保存颜色、连续重复和 RLE 连起来；目标是让观众知道这次讲什么、为什么能省空间。

English:

Hi, I’m Bingxi. Today I’m talking about a simple way to make some image data smaller, called run-length encoding, or R-L-E.

[Point to the heart.]

This heart is made of small colored squares called pixels. To store the picture, a computer needs to record the color of every pixel.

But many nearby pixels have the same color. RLE groups consecutive pixels of the same color and records how many there are. This can save space while keeping the picture exactly the same.

Let’s try it with one row.

Source: Original teaching examples. All size comparisons use the explicitly stated count/value format.

## Slide 2: How would you describe this row?

中文：指出 32 个像素和四种颜色，再让观众用简短语言描述。不要提前念出八个 A；问完停 5–10 秒。

English:

Here are thirty-two pixels. A, B, C, and D are labels for four different colors.

For this example, each pixel’s color uses one byte, so this row takes thirty-two bytes.

[Point across the row.]

If you were describing this row to someone, how could you say it without naming every pixel separately?

[Wait 5–10 seconds for an answer. Then advance.]

Here is a shorter description.

Source: Original 32-pixel example. The encoded answer is only on the next slide.

## Slide 3: A row becomes four runs

中文：先说四段，再指 (8,A) 分别解释次数和颜色。每对两字节，四对八字节，节省 75%。这里只比较像素数据。

English:

Eight As, eight Bs, eight Cs, and eight Ds. That gives us four groups. In RLE, each group of consecutive equal values is called a run.

[Point to the first pair.]

This pair means “eight pixels of color A.” The first number tells us how many. The second part tells us the color.

For this demo, the count and the color each use one byte. So each pair takes two bytes, and four pairs take eight bytes.

We went from thirty-two bytes to eight, saving seventy-five percent. This comparison covers pixel data only. The image dimensions and the color definitions are already known.

Source: Original example verified by the companion C program: 32 raw bytes, four pairs, eight encoded bytes.

## Slide 4: Decoding restores every pixel

中文：指着上面的 (8,A) 和下面八个 A 讲解展开。无损意味着每个值和顺序都相同；程序检查长度和字节。

English:

Now let’s turn those pairs back into pixels. That process is called decoding.

[Point from the first pair to the row below.]

Eight and A tells us to write A eight times. We do the same for B, C, and D.

We get exactly the original values, in the original order. That is what lossless means.

The C program checks this by comparing the restored length and every restored byte with the original data.

Source: The companion demo checks restored_size == n and memcmp(data, restored, n) == 0.

## Slide 5: Will this row get smaller?

中文：颜色不断交替，仍然每对两个字节。让观众猜结果更小、一样还是更大，停 5–10 秒后翻页。

English:

Now we have another row of thirty-two pixels. This time, the colors keep switching: A, B, A, B.

We still need two bytes for each count-and-color pair.

Do you think RLE will make this row smaller, keep it the same size, or make it bigger?

[Wait 5–10 seconds for an answer. Then advance.]

Let’s check.

Source: Original alternating-color example. The answer is revealed on the following slide.

## Slide 6: Compression can make data bigger

中文：指着 (1,A)(1,B) 解释每次变色就开始新的一段。32 对共 64 字节；仍能还原，但变大。

English:

It gets bigger. Every time the color changes, a new run begins. Here, each run contains only one pixel.

[Point to the pairs.]

We have one A, one B, one A, and so on. Thirty-two pixels now need thirty-two pairs. At two bytes per pair, that is sixty-four bytes, twice the original size.

We can still recover every pixel correctly, so it is still lossless. But the extra count bytes make this particular input larger.

Source: Companion C experiment: alternating input has 32 raw bytes and 64 encoded bytes, with exact reconstruction.

## Slide 7: ABAB still has a pattern

中文：ABAB 仍有重复规律。上方是单个值重复，下方是整个片段重复。LZ77 能引用前面出现的字节串；不计算也不声称测过它的大小。

English:

Look at the alternating row again. It still has a pattern: AB keeps repeating.

Our RLE counts consecutive copies of one value, like the As at the top.

[Point to the AB groups.]

A method called L-Z seventy-seven can use repeated sequences. It refers back to matching data that appeared earlier, so it can take advantage of this repeated AB pattern.

The exact space saving depends on how it stores those references. My demo implements RLE, not LZ77.

Source: RFC 1951, section 3.2: repeated strings may be represented by a backward distance and length. https://www.rfc-editor.org/rfc/rfc1951.html . This slide is a conceptual comparison, not an implementation or size benchmark of LZ77.

## Slide 8: Same size. Different patterns.

中文：按左中右依次指图，解释 2、19、144 段如何得到 4、38、288 字节。背景也计入；跨行可以继续同一段。

English:

Here is how our RLE behaves on three pictures. They all have twelve by twelve pixels, or one hundred and forty-four bytes before encoding.

We read each row from left to right, continuing onto the next row. Our format allows a run to cross that boundary.

[Point left, then middle, then right.]

The left picture has two large blocks of color. That means two runs, or four bytes.

The heart has nineteen runs, counting both the heart and its background, so it takes thirty-eight bytes.

On the right, every next pixel changes color. That produces one hundred and forty-four pairs, or two hundred and eighty-eight bytes.

The same image dimensions can give very different results.

Source: Companion C program counts 2, 19, and 144 encoded pairs for the three 144-byte patterns.

## Slide 9: RLE works best with long runs

中文：总结适用条件，连接 C 数组扫描、相邻比较、计数与输出。一字节次数最多 255，更长需拆段。最后停下来接受提问。

English:

The main lesson is that RLE works best with long stretches of the same value.

This also connects directly to C. The encoder walks through an array, compares neighboring values, counts a run, and writes a count-and-value pair into another array.

Our stored count is one unsigned byte, so the maximum is two hundred and fifty-five. If a run is longer, we split it into more pairs.

For these examples, compression can save space or make the data larger. We need to check the result for the actual input.

Thank you. What questions do you have?

Source: Companion implementation in src/rle.c and the 300-copy split experiment in src/demo.c.
