# Module Design

## Overview

The design notes for this module are mainly a review of a few different attenuverter topologies, comparing them according to

* linearity
* input impedance
* gain scaling (maintaining $v_o =0 $ when $\alpha = 1/2$)

In the derivations below, $0 \leq \alpha \leq 1$ is the position of the potentiometer wiper, which I assume has a linear taper. $\alpha=1/2$ corresponds to the mid-point, which is treated as the null ($v_o = 0$ when $\alpha = 1/2$)

Topology | Gain scailing | Transfer function | Input impedance | Non-linearity
---------|---------------|-------------------|-----------------|---------------
Basic    | None ($G=1$)  | $H(\alpha)=2\alpha - 1$   | $R_{in} \geq R_1 \parallel R_g$ | $\epsilon(\alpha) = 0$
Balanced | Fixed $G>0^\mathrm{(1)}$     | $H(\alpha) = G\frac{(2\alpha -1)(3+3G-2\alpha)}{(3-2\alpha)(1+G+2G\alpha)}$ | $R_{in} \geq R_g\frac{1+G}{1+3G}$ | $\epsilon(\alpha) = \frac{4G^2\alpha(\alpha - 1)(2\alpha - 1)}{(3-2\alpha)(1+G+2G\alpha)} $
Precision | None ($G=1$) | $H(\alpha) = 2A(\alpha)-1$ | $R_{in} \geq R_1 \parallel (R_g \parallel R_s)$ | $\epsilon(\alpha) = \frac{4\alpha^3 - 6\alpha^2  + 2\alpha }{2\alpha(1 - \alpha) + R_s/R_g}$

**Note 1**: $G$ can be selected by a choice of $R_g$, but cannot be adjusted dynamically. See the component selection notes in the corresponding section for details.

**Aside**: I clearly did the derivation after building the circuit, otherwise I wouldn't have used a second opamp to add 2x gain in this design.

## Basic

The simplest version can be found in *The Art of Electronics* (section 4.3.1).

$$\begin{align*}
v_p &= v_i\frac{\alpha R_g}{R_g} = \alpha v_i \\
0 &= \frac{v_o - v_n}{R_f} + \frac{v_i - v_n}{R_1} \\
\to v_o &= v_p - \frac{R_f}{R_1}(v_i - v_p) \\
        &= \alpha v_i - \frac{R_f}{R_1}\left(1 - \alpha\right)v_i \\
\mathrm{let}\ G = \frac{R_f}{R_1} \to v_o &= \left[\alpha - G\left(1 - \alpha\right)\right]v_i \\
        &= \left[\alpha \left(G + 1\right) - G\right]v_i
\end{align*}$$

Consequently, the response is linear in $\alpha$. 

There is only one value for $G$ that results in the null at $\alpha = 1/2$:

$$\begin{align*}
0 &= \left[\frac{1}{2}\left(G + 1\right) - G\right]\\
\to G &= 1 
\end{align*}$$

When $G=1$ ($R_f = R_1$) the formula reduces to

$$\begin{align*}
v_o &= \left(2\alpha - 1\right)v_i
\end{align*}$$

The input impedance can be understood by ideal opamp analysis: the virtual connection $v_n = v_p$ means that $R_1$ is connected in parallel to the upper leg of the potentiometer $(1-\alpha)R_g$.

$$\begin{align*}
R_{in} &= R_1 || (1-\alpha)R_g + \alpha R_g\\
\to R_{in} &\geq R_1 || R_g
\end{align*}$$

## Balanced

This version, implemented in the SSI module, is used in the Befaco [Dual Attenuverter](https://www.befaco.org/dual-atenuverter/) design. The input is fed into branches leading to both inputs of the opamp.

For the analysis, start with KCL at the opamp inputs

$$\begin{align*}
\mathrm{@vp:}\quad 0 &= \frac{v_i - v_p}{R_p} - \frac{v_p}{\alpha R_g} \\
\to v_p &= v_i \frac{\alpha R_g}{R_p+\alpha R_g} \\
\mathrm{@vn:}\quad 0 &= \frac{v_i - v_n}{R_n} - \frac{v_n}{R_1 + (1-\alpha)R_g} + \frac{v_o - v_n}{R_f} \\
\to \frac{v_i}{R_n}  + \frac{v_o}{R_f} &=  v_n\left(\frac{1}{R_n} + \frac{1}{R_1 + (1-\alpha)R_g} + \frac{1}{R_f}\right) \\
\to \frac{v_o}{R_f} &= v_n\left(\frac{1}{R_n} + \frac{1}{R_1 + (1-\alpha)R_g} + \frac{1}{R_f}\right) - \frac{v_i}{R_n} \\
\mathrm{with}\ v_n=v_p:\quad v_o &= v_i\left[ \frac{\alpha R_g R_f}{R_p+\alpha R_g}\left(\frac{1}{R_n} + \frac{1}{R_1 + (1-\alpha)R_g} + \frac{1}{R_f}\right) - \frac{R_f}{R_n} \right] \\
&= v_i\left[ \frac{\alpha R_g R_f}{R_p+\alpha R_g}\left(\frac{(R_f+R_n)[R_1 + (1-\alpha)R_g] + R_n R_f}{R_n R_f [R_1 + (1-\alpha)R_g]}\right) - \frac{R_f}{R_n} \right] \\
&= v_i\frac{R_f}{R_n}\left[ \frac{\alpha R_g}{R_p+\alpha R_g}\left(\frac{(R_f+R_n)[R_1 + (1-\alpha)R_g] + R_n R_f}{R_f [R_1 + (1-\alpha)R_g]}\right) - 1 \right] \\
\end{align*}$$

Assuming that we want the same gain at each endpoint ($-G \leq v_o/v_i \leq +G$), we have three constraints at $G = \pm 1,\ 0$:

$$\begin{align*}
\alpha=0:\quad -G &= -\frac{R_f}{R_n}\\
\alpha=\frac{1}{2}:\quad 0 &= \frac{R_f}{R_n}\left[ \frac{R_g}{2R_p+ R_g}\left(\frac{(R_f+R_n)(R_1 + R_g/2) + R_n R_f}{R_f (R_1 + R_g/2)}\right) - 1 \right] \\
\to 1 &= \frac{R_g}{2R_p+R_g}\left(R_f+R_n +\frac{R_n R_f}{R_1 + R_g/2}\right)\frac{1}{R_f}\\
\alpha=1:\quad G &= \frac{R_f}{R_n}\left[ \frac{R_g}{R_p+R_g}\left(\frac{(R_f+R_n)R_1 + R_n R_f}{R_f R_1}\right) - 1 \right] \\
\to 2 &= \frac{R_g}{R_p+R_g}\frac{1}{R_1 R_f}\left[(R_f+R_n)R_1 + R_n R_f\right] \\
\end{align*}$$

Combining the latter pair of equations,

$$\begin{align*}
\frac{R_g}{2R_p+R_g}\left(R_f+R_n +\frac{R_n R_f}{R_1 + R_g/2}\right)
&= \frac{R_g}{R_p+R_g}\frac{1}{2R_1}\left[(R_f+R_n)R_1 + R_n R_f\right]
\end{align*}$$

let $R_1 = R_g/2$. First note that when $\alpha=1/2$

$$\begin{align*}
1 &= \frac{R_g}{2R_p+R_g}\left(R_f+R_n +\frac{R_n R_f}{R_1 + R_g/2}\right)\frac{1}{R_f}\\
\to R_f(2R_p + R_g) &= R_g\left(R_f + R_n + \frac{R_n R_f}{R_g}\right)\\ 
\to 2R_fR_p + R_f R_g &= R_g R_f + R_g R_n + R_n R_f\\ 
\to 2R_fR_p &= R_g R_n + R_n R_f\\ 
\to R_p &= R_n\frac{R_g + R_f}{2R_f}
\end{align*}$$

Next, continuing with the equality between the $\alpha=0$ and $\alpha=1$ equations,

$$\begin{align*}
\frac{2R_1}{2R_p+2R_1}\left(R_f+R_n +\frac{R_n R_f}{R_1 + R_1}\right)
&= \frac{2R_1}{R_p+2R_1}\frac{1}{2R_1}\left[(R_f+R_n)R_1 + R_n R_f\right] \\
\to \frac{R_1}{R_p+R_1}\left(R_f+R_n +\frac{R_n R_f}{2R_1}\right)
&= \frac{2R_1}{R_p+2R_1}\frac{1}{2R_1}\left(R_f R_1 + R_n R_1 + R_n R_f\right) \\
\to \frac{1}{R_p+R_1}\left(2R_f R_1+2R_n R_1 +R_n R_f\right)
&= \frac{2}{R_p+2R_1}\left(R_f R_1 + R_n R_1 + R_n R_f\right) \\
\to \frac{2}{R_p+R_1}\left(R_f R_1 + R_n R_1 + R_n R_f \right)- \frac{R_n R_f}{R_p+R_1}
&= \frac{2}{R_p+2R_1}\left(R_f R_1 + R_n R_1 + R_n R_f \right) \\
\to \frac{R_n R_f}{R_p+R_1}
&= 2\left(\frac{R_1}{(R_p+R_1)(R_p+2R_1)}\right)\left(R_f R_1 + R_n R_1 + R_n R_f \right) \\
\to \frac{R_n R_f}{R_p+R_1}
&= 2R_1\left(\frac{R_f R_1 + R_n R_1 + R_n R_f}{R_p^2+3R_1R_p+2R_1^2}\right) \\
\to R_n R_f (R_p^2+3R_1R_p+2R_1^2) &= 2R_1 (R_f R_1 + R_n R_1 + R_n R_f) (R_p+R_1) \\
\to R_p^2R_n R_f + 3R_1R_pR_n R_f+2R_1^2R_n R_f &=  2 R_1^2 R_p R_f + 2R_1^2 R_p R_n  + 2R_1 R_p R_n R_f + 2R_1^3 R_f  + 2R_1^3R_n  + 2R_1^2R_n R_f \\
\to R_p^2R_n R_f + R_1R_pR_n R_f &=  2 R_1^2 R_p R_f + 2R_1^2 R_p R_n  + 2R_1^3 R_f  + 2R_1^3R_n \\
\to R_pR_n R_f(R_p + R_1) &=  2 R_1^2 R_p( R_f + R_n ) + 2R_1^3 (R_f  + R_n) \\
\to R_pR_n R_f(R_p + R_1) &=  2 R_1^2 (R_p + R_1)( R_f + R_n ) \\
\to R_pR_n R_f &=  2 R_1^2 ( R_f + R_n ) \\
\to R_p &= 2 R_1^2 \frac{ R_f + R_n }{R_f R_n} \\
\end{align*}$$

Combining $R_p = R_p$

$$\begin{align*}
R_n\frac{R_g + R_f}{2R_f} &= 2 R_1^2 \frac{ R_f + R_n }{R_f R_n} \\
\to R_n^2(R_g + R_f) &= 4 R_1^2 ( R_f + R_n ) = R_g^2 ( R_f + R_n ) \\
0 &= (R_g^2 - R_n^2)R_f + R_g^2R_n - R_n^2R_g \\
0 &= (R_g - R_n)(R_g + R_n)R_f + (R_g - R_n)R_gR_n \\
  &= (R_g - R_n)[(R_g + R_n)R_f + R_gR_n] \\
\end{align*}$$

such that $R_g = R_n$. This gives a set of constraints to determine the gain:

$$\begin{align*}
\mathrm{let}\quad R_1 &= \frac{R_g}{2} \\
\mathrm{with}\quad G &= \frac{R_f}{R_n} \\
\to R_n &= R_g = \frac{R_f}{G} \to R_f = G R_g \\
\to R_p &= R_n\frac{R_g + R_f}{2R_f} = \left(\frac{1+G}{2G}\right)R_g
\end{align*}$$

Choose 

1. the desired maximum gain $G$
2. the potentiometer resistance $R_g$

Calculate 

1. $R_1 = \frac{R_g}{2}$
2. $R_n = R_g$
3. $R_p = \left(\frac{1+G}{2G}\right)R_g$
4. $R_f = GR_g$

Note that for $G=1$, all resistances $R_n=R_p=R_g=R_f=R$ and $R_1 = R/2$. Applying these relationships to the transfer function

$$\begin{align*}
H(\alpha) = \frac{v_o}{v_i} &= \frac{R_f}{R_n}\left[ \frac{\alpha R_g}{R_p+\alpha R_g}\left(\frac{(R_f+R_n)[R_1 + (1-\alpha)R_g] + R_n R_f}{R_f [R_1 + (1-\alpha)R_g]}\right) - 1 \right] \\
&= G\left[ \frac{\alpha R_g}{G'R_g+\alpha R_g}\left(\frac{(G R_g + R_g)[R_g/2 + (1-\alpha)R_g] + GR_g^2 }{G R_g [R_g/2 + (1-\alpha)R_g]}\right) - 1 \right] \\
&= G\left[ \frac{\alpha }{G'+\alpha}\left(\frac{(G + 1)[1 + 2(1-\alpha)] + 2G }{G [1 + 2(1-\alpha)]}\right) - 1 \right] \\
&= G\left[ \frac{\alpha }{G'+\alpha}\left(\frac{1 + 2(1-\alpha)(G + 1) + 3G }{G + 2G(1-\alpha)}\right) - 1 \right] \\
&= G\left[ \frac{2\alpha }{1+G +2G\alpha}\left(\frac{3 + 5G -2G\alpha -2\alpha}{3 - 2\alpha}\right) - 1 \right] \\
&= G\left[ \frac{2\alpha }{3 - 2\alpha}\left(\frac{3 + 5G -2G\alpha -2\alpha}{1+G +2G\alpha}\right) - 1 \right] \\
&= G\left[ \frac{2\alpha }{3 - 2\alpha}\left(\frac{3 + 5G -2G\alpha -2\alpha}{1+G +2G\alpha}\right) - 1 \right] \\
\end{align*}$$

With a common denominator, the numerator becomes

$$\begin{align*}
\to N &= 6\alpha + 10G\alpha -4G\alpha^2 - 4\alpha^2 - (3-2\alpha)(1+G+2G\alpha)\\
      &= 6\alpha + 10G\alpha -4G\alpha^2 - 4\alpha^2 - 3 - 3G - 6G\alpha +2\alpha +2G\alpha +4G\alpha^2\\
      &= 6G\alpha - 4\alpha^2  + 8\alpha -3G -3\\
      &= 6G\alpha - 3G -4\alpha^2 + 2\alpha + 6\alpha - 3\\
      &= (2\alpha -1)(3G -2\alpha +3)\\
\end{align*}$$

and the transfer function is then

$$\begin{align*}
H(\alpha) &= G\frac{(2\alpha -1)(3+3G-2\alpha)}{(3-2\alpha)(1+G+2G\alpha)}
\end{align*}$$

To confirm, it must statisfy the conditions for $\alpha = 0, 1/2, 1$

$$\begin{align*}
H(0) &= -G = G\frac{-(3+3G)}{3(1+G)} \\
H(1/2) &= \quad 0 = G\frac{(1 - 1)(3+3G-1)}{(3-1)(1+G+G)}\\
H(1) &= +G = G\frac{(2 -1)(3+3G-2)}{(3-2)(1+G+2G)} = G\frac{1+3G}{1+3}
\end{align*}$$

The non-linearity is defined relative to the linear transfer function $H_0(\alpha) = G(2\alpha - 1)$:

$$\begin{align*}
\epsilon(\alpha) = H - H_0 &= G\frac{(2\alpha -1)(3+3G-2\alpha)}{(3-2\alpha)(1+G+2G\alpha)} - G(2\alpha - 1) \\
&= G(2\alpha - 1)\left[\frac{3+3G-2\alpha}{(3-2\alpha)(1+G+2G\alpha)} - 1\right] \\
&= \frac{4G^2\alpha(\alpha - 1)(2\alpha - 1)}{(3-2\alpha)(1+G+2G\alpha)} \\
\end{align*}$$

which goes to zero at the control points as expected. 

Finally, the minimum input impedance is $R_{in} = R_n \parallel R_p$ when $\alpha = 0$ (this excludes the branch containing $R_1$ and $R_g$). Relative to $R_g$, this becomes

$$\begin{align*}
R_{in} \geq R_n \parallel R_p &= R_g \parallel \frac{1+G}{2G}R_g \\
&= R_g\left(1 + \frac{2G}{1+G}\right)^{-1} \\
&= R_g\frac{1+G}{1+3G}
\end{align*}$$

## Precision

This variant is highlighted in the Kassutronics page and is an extension of the basic topology. The main difference is in the relation between the input and the voltage at the non-inverting terminal of the opamp. 

$$\begin{align*}
v_p &= v_i \frac{R_{gl}} { R_{gl} + R_{gu}}
\end{align*}$$

where 

$$\begin{align*}
R_{gl} &= \alpha R_g \parallel R_s = \frac{\alpha R_g R_s}{\alpha R_g + R_s}\\
R_{gu} &= (1-\alpha R_g) \parallel R_s = \frac{(1-\alpha) R_g R_s}{(1-\alpha) R_g + R_s}\\
\end{align*}$$

Consequently, 

$$\begin{align*}
v_p &= v_i \frac{\alpha R_g R_s}{\alpha R_g + R_s} \left(\frac{\alpha R_g R_s}{\alpha R_g + R_s} + \frac{(1-\alpha) R_g R_s}{(1-\alpha) R_g + R_s}\right)^{-1} \\
&= v_i \frac{\alpha }{\alpha R_g + R_s} \left(\frac{\alpha }{\alpha R_g + R_s} + \frac{1-\alpha}{(1-\alpha) R_g + R_s}\right)^{-1} \\
\mathrm{let}\quad a &= \alpha R_g \\
\mathrm{let}\quad b &= (1-\alpha) R_g \\
\to \frac{v_p}{v_i} \equiv A(\alpha) &= \frac{\alpha }{a + R_s} \left(\frac{\alpha }{a + R_s} + \frac{1-\alpha}{b + R_s}\right)^{-1} \\
&= \frac{\alpha }{a + R_s} \left(\frac{\alpha(b+R_s) + (1-\alpha)(a+R_s)}{(a+R_s)(b+R_s)}\right)^{-1} \\
&= \alpha\frac{b+R_s}{\alpha (b- a)+ a + R_s} \\
&= \alpha\frac{(1-\alpha)R_g + R_s}{2\alpha(1 - \alpha) R_g + R_s} \\
\end{align*}$$

As in the basic topology (assuming a low-impedance input), the selection of $R_g$ (and here $R_s$) is decoupled from the feedback, and it's convenient to write

$$\begin{align*}
v_p &= v_i A(\alpha)
\end{align*}$$

Note that $A(0) = 0$ and

$$\begin{align*}
A(1/2) &= \frac{1}{2} = \frac{0.5 R_g R_s}{0.5 R_g + R_s} \left(\frac{0.5 R_g R_s}{0.5 R_g + R_s} + \frac{0.5 R_g R_s}{0.5 R_g + R_s}\right)^{-1}\\
A(1) &= 1 = \frac{R_g R_s}{R_g + R_s} \left(\frac{R_g R_s}{R_g + R_s}\right)^{-1}
\end{align*}$$

From here, the derivation is the same. Starting with KCL at $v_n$ and applying the constraint $R_f = R_1$

$$\begin{align*}
0 &= \frac{v_o - v_n}{R_f} + \frac{v_i - v_n}{R_1} \\
\to v_o &= v_p - \frac{R_f}{R_1}(v_i - v_p) \\
        &= A(\alpha) v_i - \left(1 - A(\alpha)\right)v_i \\
\to H(\alpha) = \frac{v_o}{v_i} &= 2A(\alpha) - 1        
\end{align*}$$

The non-linearity is then

$$\begin{align*}
\epsilon(\alpha) = H - H_0 &= 2[A(\alpha) - \alpha]\\
&= 2\alpha\left[\frac{(1-\alpha)R_g + R_s}{2\alpha(1 - \alpha) R_g + R_s} - 1\right]\\
&= 2\alpha\left[\frac{(1-\alpha)R_g + R_s - 2\alpha(1 - \alpha) R_g - R_s}{2\alpha(1 - \alpha) R_g + R_s}\right]\\
&= 2\alpha R_g\left[\frac{1 - 3\alpha + 2\alpha^2 }{2\alpha(1 - \alpha) R_g + R_s}\right]\\
&= \frac{4\alpha^3 - 6\alpha^2  + 2\alpha }{2\alpha(1 - \alpha) + R_s/R_g}\\
\end{align*}$$

with the checks confirming the response at the control points

$$\begin{align*}
\epsilon(0) &= 0\\
\epsilon(1/2) &= 0 = \left[\frac{1 - 3/2 + 1/2 }{1/2 + \frac{R_s}{R_g}}\right]\\
\epsilon(1) &= 0 = 2 \left[\frac{1 - 3 + 2 }{\frac{R_s}{R_g}}\right]\\
\end{align*}$$

From the error expression $\epsilon(\alpha) \to 0$ as $R_s \to \infty$. In the other direction, for $R_s \ll R_g$ when $\alpha > 0$ and $\alpha < 1$, 

$$\begin{align*}
\epsilon(\alpha) &\to \frac{2\alpha^2 - 3\alpha  + 1 }{1 - \alpha}\\
&= \frac{(2\alpha - 1)(\alpha - 1)}{1 - \alpha} \\
&= 1-2\alpha \\
\end{align*}$$

Around $\alpha = 1/2$, the error will increase as $1-2\alpha$, but as $\alpha \to 0$ or $\alpha \to 1$, the assumption about the smallness of $R_s/R_g$ breaks down and the error returns to 0. This generates the "quasi-logarithmic" behaviour.

Finally, the input impedance has a minimum at $\alpha = 0$, where 

$$ 
R_{in} = (R_g \parallel R_s) \parallel R_1
$$

## References

1. Horowitz & Hill, *The Art of Electronics*, 3rd. ed.,  Cambridge University Press (2016)
2. Kassutronics, "Precision Attenuverter/Mixer," [kassu2000.blogspot.com](https://kassu2000.blogspot.com/2018/04/precision-attenuverter-mixer.html)
2. Befaco, "Dual Attenuverter," [befaco.org](https://www.befaco.org/dual-atenuverter/)
