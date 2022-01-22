# GAN
这是我GAN项目的学习过程

## 最初的GAN(2014 Ian Goodfellow)
Ian Goodfellow 等人在 2014 年的论文中提出了生成式对抗网络，尽管这个想法几乎立刻使研究人员们兴奋不已，但还是花了几年时间才克服了 GAN 的一些困难。就像许多伟大的想法一样，事后看起来似乎很简单:让神经网络相互竞争，希望这种竞争能够促使它们变得更好。
* 生成器:以随机分布作为输入 (通常是高斯分布)，并输出一些数据 (通常是图像)。可以将随即输入视为要生成图像的潜在表征 (即编码)。因此生成器提供的功能与变分自动编码器中的解码器相同，并且可以使用相同的方式来生成新图像。但它们的训练方式大不相同。
* 判别器:输入从生成器得到的伪图像或从训练集中得到的真实图像，并且必须猜测输入图像是伪图像还是真实图像。
 
在训练过程中，生成器和判别器有相反的目标:判别器试图从真实图像中分辨出虚假图像，而生成器则试图长生看起来足够真实的图像来欺骗判别器。由于 GAN 由不同目标的两个网络组成，因此无法像常规网络一样对其进行训练。每个训练迭代都分为两个阶段:
* 在第一阶段，我们训练判别器。从训练集中采样一批真实图像，再加上用生成器生成的相等数量的伪图像组成训练批次。对于伪图像，将标签设置为 0;对于真是图像，叫标签设置为 1，并使用二元交叉熵损失在该被标签的批次上对判别器进行训练。重要的是，在这个阶段反向传播**只能优化判别器的权重**。
* 在第二阶段，我们训练生成器。首先使用它来生成另一批伪图像，然后再次使用判别器来判断图像是伪图像还是真实图像。在这个批次中不添加真实图像，并且所有标签都设置为 1(真实)，即我们希望生成器能产生判别器会 (错误地) 认为是真实的图像!至关重要的是，在此步骤中，**判别器的权重会被固定，因此反向传播只会影响生成器的权重**。

生成器实际上从未看到过任何真实的图像，但是它逐渐学会产生令人信服的伪图像!它所得到的是流经判别器的回流梯度。幸运的是，判别器越好，这些二手梯度中包含的真实图像信息就越多，因此生成器可以取得很大进步。下面为 Fashion MNIST 构建一个简单的 GAN:
