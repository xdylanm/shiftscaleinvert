# Design Notes


## Attenuverter Again

At $V_n$:
$$
\begin{align*}
0 &= \frac{V_o-V_n}{R_f} - \frac{V_n}{R_a} + \frac{V_i - V_n}{R_n} \\
\to V_n&\left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R_n}\right) = \frac{V_o}{R_f} + \frac{V_i}{R_n} \\
\to V_n &= \left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R_n}\right)^{-1}\left(\frac{V_o}{R_f} + \frac{V_i}{R_n}\right)
\end{align*}
$$

At $V_p$:
$$
\begin{align*}
0 &= \frac{V_i - V_p}{R_p} - \frac{V_p}{R'_a} \\
\to V_p&\left(\frac{1}{R_p} + \frac{1}{R'_a}\right) = \frac{V_i}{R_p} \\
\to V_p &= \left(\frac{1}{R_p} + \frac{1}{R'_a}\right)^{-1}\frac{V_i}{R_p}
\end{align*}
$$

Using $V_n = V_p$
$$
\begin{align*}
\left(\frac{1}{R_p} + \frac{1}{R'_a}\right)^{-1}\frac{V_i}{R_p} &= \left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R_n}\right)^{-1}\left(\frac{V_o}{R_f} + \frac{V_i}{R_n}\right) \\
\to \frac{V_o}{R_f} + \frac{V_i}{R_n} &= \left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R_n}\right)\left(\frac{1}{R_p} + \frac{1}{R'_a}\right)^{-1}\frac{V_i}{R_p} \\
\to V_o  &= R_f\left[\left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R_n}\right)\left(\frac{1}{R_p} + \frac{1}{R'_a}\right)^{-1}\frac{1}{R_p} - \frac{1}{R_n}\right]V_i
\end{align*}
$$

Let $R_n = R_p = R$
$$
\begin{align*}
V_o  &= \frac{R_f}{R}\left[\left(\frac{1}{R_f}+\frac{1}{R_a}+\frac{1}{R}\right)\left(\frac{1}{R} + \frac{1}{R'_a}\right)^{-1} - 1\right]V_i
\end{align*}
$$




