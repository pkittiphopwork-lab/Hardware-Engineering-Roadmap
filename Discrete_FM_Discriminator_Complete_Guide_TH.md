# Discrete FM Discriminator: คู่มืออธิบายตั้งแต่ FM, Phase, I/Q, R/J ถึงการออกแบบ FPGA

**ภาษา:** ไทย (คงศัพท์วิศวกรรมภาษาอังกฤษ)  
**ระดับ:** DSP / Communications / FPGA & RTL Design  
**ขอบเขต:** Discrete-time FM demodulation โดยใช้ complex baseband ไม่ใช่วงจรอนาล็อกแบบ Foster–Seeley/Ratio Detector  
**เอกสารต้นทาง:** บทสรุปและสมการที่ให้มาในบทสนทนา และบทความ *FM Demodulation of a 10.7 MHz IF Frequency* [1]  
**แนวทาง:** แสดงที่มาและข้อสมมติของทุกสมการ แยกสมการที่เที่ยงตรงออกจากการประมาณ

> **สรุปในหนึ่งประโยค:** FM เก็บข้อมูลในอัตราการเปลี่ยนเฟส; I/Q ใช้แทนเฟสเป็นเวกเตอร์เชิงซ้อน; การคูณ sample ปัจจุบันกับ complex conjugate ของ sample ก่อนหน้าให้ผลลัพธ์ `R + jJ` ซึ่งมีมุมเท่ากับ phase difference และสามารถแปลงกลับเป็นความถี่ได้

---

## สารบัญ

1. นิยามและสัญลักษณ์
2. FM คืออะไร และทำไมต้องตรวจจับความถี่
3. ที่มาของ Phase และอนุพันธ์ dφ/dt
4. Real RF signal, analytic signal และ complex baseband: ใช้อะไรตอนไหน
5. I/Q คืออะไร และมองเป็นเวกเตอร์อย่างไร
6. ทำไม FM receiver ต้องใช้ข้อมูลมากกว่าหนึ่ง sample
7. ที่มาของ R และ J จาก complex conjugate multiplication
8. ที่มาของ R และ J จาก dot product / cross product
9. ทำไมต้องใช้ atan2(J,R) แทน atan(J/R)
10. พิสูจน์ dφ/dt จาก I/Q โดยตรง
11. ความสัมพันธ์ของ derivative, finite difference และ J-only
12. ตัวอย่างตัวเลขพร้อมคำนวณทีละขั้น
13. วิเคราะห์บทความ IF 10.7 MHz และข้อควรระวังในการอ่าน RTL
14. สถาปัตยกรรม FPGA/ASIC, fixed-point และการ Verification
15. ข้อจำกัดและความผิดพลาดที่พบบ่อย
16. สรุปสมการและแหล่งอ้างอิง

---

## 1. นิยามและสัญลักษณ์

| สัญลักษณ์ | ความหมาย | หน่วย/หมายเหตุ |
|---|---|---|
| $m(t)$ | Message หรือ modulating signal | ขึ้นกับนิยาม; ในตัวอย่าง normalize เป็น ±1 |
| $f_c$ | RF/IF carrier frequency | Hz |
| $k_f$ | Frequency sensitivity | Hz ต่อหน่วย $m(t)$ |
| $f_i(t)$ | Instantaneous frequency | Hz |
| $\phi(t)$ | Phase (โดยมากหมายถึง **unwrapped phase**) | rad |
| $\theta(t)$ | Modulation/baseband phase | rad |
| $A(t)$ | Magnitude ของ complex signal | ไม่เป็นลบ |
| $I(t),Q(t)$ | In-phase / Quadrature components | Real-valued signals |
| $j$ | Imaginary unit | $j^2=-1$ |
| $f_s$ | Sampling frequency ณ จุดที่เข้า discriminator | samples/s |
| $T_s$ | Sampling period | $T_s=1/f_s$ |
| $n$ | ดัชนี sample | Integer |
| $x[n]$ | Complex baseband sample | $I[n]+jQ[n]$ |
| $R[n],J[n]$ | Real/Imag parts ของ **ผลคูณระหว่างสอง samples** | ไม่ใช่ I/Q ชุดใหม่จาก ADC |
| $\Delta\phi[n]$ | Phase difference จาก sample $n-1$ ไป $n$ | rad/sample |

**เรื่องเครื่องหมาย:** ในเอกสารนี้นิยาม complex envelope เป็น $x=I+jQ$ และคูณในลำดับ $x[n]x^*[n-1]$ ดังนั้นความถี่ baseband บวกทำให้ $J$ เป็นบวกในช่วงที่ $|\Delta\phi|<\pi$; หากสลับนิยาม Q หรือสลับลำดับการคูณ เครื่องหมายอาจกลับกัน

## 2. FM คืออะไร และทำไมต้องตรวจจับความถี่

สัญญาณ carrier ที่ยังไม่ถูก modulate:

$$
s_c(t)=A_c\cos(2\pi f_ct+\phi_0)
$$

FM ทำให้ **instantaneous frequency** เปลี่ยนตาม $m(t)$:

$$
\boxed{f_i(t)=f_c+k_fm(t)}
$$

ถ้า $m(t)$ ถูก normalize เป็น ±1 และ $k_f=5\,\text{kHz}$; เมื่อ $f_c=100\,\text{kHz}$:

| $m(t)$ | $f_i(t)$ |
|---:|---:|
| +1 | 105 kHz |
| +0.5 | 102.5 kHz |
| 0 | 100 kHz |
| −0.5 | 97.5 kHz |
| −1 | 95 kHz |

ดังนั้น receiver ต้องประเมิน $f_i(t)$ แล้วลบ $f_c$ และหารด้วย $k_f$ เพื่อกู้คืน $m(t)$ (ในทางปฏิบัติต้องพิจารณา frequency offset, noise, filtering และ de-emphasis ตามระบบ)

## 3. ที่มาของ Phase และอนุพันธ์ dφ/dt

### 3.1 Phase ของคลื่นไซน์

สำหรับความถี่คงที่:

$$
s(t)=A\cos(2\pi ft+\phi_0),\qquad \phi(t)=2\pi ft+\phi_0
$$

หนึ่งรอบเท่ากับ $2\pi$ rad และ 1 Hz หมายถึงครบหนึ่งรอบต่อวินาที ความเร็วเชิงมุมจึงเท่ากับ $2\pi f$ rad/s

$$
\frac{d\phi(t)}{dt}=2\pi f
\quad\Longrightarrow\quad
\boxed{f=\frac{1}{2\pi}\frac{d\phi(t)}{dt}}
$$

สำหรับ FM ซึ่งความถี่ไม่คงที่ เราใช้นิยามทั่วไป:

$$
\boxed{f_i(t)=\frac{1}{2\pi}\frac{d\phi(t)}{dt}}
$$

อนุพันธ์หมายถึงความชันของกราฟ phase ต่อเวลา:

$$
\frac{d\phi(t)}{dt}=\lim_{\Delta t\to0}\frac{\phi(t+\Delta t)-\phi(t)}{\Delta t}
$$

ถ้า phase เพิ่มขึ้น $\pi/2$ rad ภายใน 1 ms อย่างสม่ำเสมอ จะได้ความเร็วเชิงมุมเฉลี่ย $500\pi$ rad/s หรือความถี่เฉลี่ย $250$ Hz ข้อความนี้หมายถึงความถี่ **เฉลี่ยในช่วงนั้น** ไม่จำเป็นต้องเป็นความถี่ ณ ทุกขณะเมื่อ phase ไม่ได้เพิ่มแบบเส้นตรง

### 3.2 พิสูจน์สมการ FM จากการอินทิเกรตความถี่

เมื่อกำหนด $f_i(t)=f_c+k_fm(t)$:

$$
\frac{d\phi(t)}{dt}=2\pi[f_c+k_fm(t)]
$$

อินทิเกรตจะได้:

$$
\boxed{\phi_{\rm RF}(t)=2\pi f_ct+2\pi k_f\int_0^t m(\tau)\,d\tau+\phi_0}
$$

รูปคลื่น FM ที่เป็น **real physical passband waveform** จึงเป็น:

$$
\boxed{s_{\rm RF}(t)=A_c\cos\!\left(2\pi f_ct+2\pi k_f\int_0^tm(\tau)d\tau+\phi_0\right)}
$$

ถ้ารู้อนุพันธ์ของ total phase ก็ถอดข้อมูลได้:

$$
\boxed{m(t)=\frac{1}{k_f}\left[\frac{1}{2\pi}\frac{d\phi_{\rm RF}}{dt}-f_c\right]}
$$

## 4. Real RF, analytic signal และ complex baseband: แตกต่างกันอย่างไร

### 4.1 รูปคลื่นที่วัดได้จริงเป็นจำนวนจริง

สัญญาณ RF ที่เสาอากาศหรือ ADC ขา analog เป็น real-valued voltage/current:

$$
s_{\rm RF}(t)=A_c\cos[2\pi f_ct+\theta(t)]
$$

**อย่าสรุปว่าแรงดัน RF จริงมีค่า $jQ$ ทางกายภาพ** เพราะสัญญาณไฟฟ้าจริงมีค่า real แต่สามารถสร้างแบบจำลองเชิงซ้อนเพื่อจัดการ phase ได้

### 4.2 Complex representation และ Euler's formula

สมการของ Euler:

$$
e^{j\alpha}=\cos\alpha+j\sin\alpha
$$

ถ้าเรานิยาม analytic/complex representation:

$$
z_{\rm RF}(t)=A_ce^{j[2\pi f_ct+\theta(t)]}
$$

จะได้ $s_{\rm RF}(t)=\mathrm{Re}\{z_{\rm RF}(t)\}$ ตาม convention นี้

### 4.3 หลัง Digital Downconversion (DDC)

เลื่อน carrier ออกด้วย complex mixer $e^{-j2\pi f_ct}$ และกรองภาพความถี่ที่ไม่ต้องการ เหลือ complex baseband (อุดมคติ):

$$
\boxed{x_{\rm BB}(t)=A(t)e^{j\theta(t)}}
$$

**สมการที่ผู้ใช้ถามก่อนหน้านี้ถูกต้อง** เมื่อ $x(t)$ หมายถึง complex signal:

$$
\boxed{x(t)=A(t)\cos\phi(t)+jA(t)\sin\phi(t)}
$$

แต่ถ้า $x(t)$ หมายถึง real RF waveform จะเขียนเท่ากันแบบนี้ไม่ได้ ต้องระบุชนิดของสัญญาณให้ชัด

**Amplitude normalization / mixer gain:** ในการ Implement จริง output baseband อาจมีตัวคูณ 1/2 หรือ gain อื่นจาก mixer และ LPF ทั้งนี้ขึ้นกับนิยาม LO และการ Scaling; รูป $Ae^{j\theta}$ เป็นรูปแบบเชิงแนวคิดหลังรวม gain แล้ว

### 4.4 การสร้าง RF กลับจาก I/Q

ให้ $x_{\rm BB}=I+jQ$ จะได้:

$$
\boxed{s_{\rm RF}(t)=\mathrm{Re}\{[I(t)+jQ(t)]e^{j2\pi f_ct}\}}
$$

หรือ

$$
\boxed{s_{\rm RF}(t)=I(t)\cos(2\pi f_ct)-Q(t)\sin(2\pi f_ct)}
$$

## 5. I/Q คืออะไร และมองเป็นเวกเตอร์อย่างไร

จาก $x(t)=A(t)e^{j\phi(t)}=I(t)+jQ(t)$:

$$
\boxed{I(t)=A(t)\cos\phi(t),\qquad Q(t)=A(t)\sin\phi(t)}
$$

Magnitude และ phase:

$$
\boxed{A(t)=\sqrt{I(t)^2+Q(t)^2}}
$$

$$
\boxed{\phi(t)=\operatorname{atan2}(Q(t),I(t))}
$$

มอง $I$ เป็นแกน X, $Q$ เป็นแกน Y แล้วเวกเตอร์ $[I,Q]^T$ มีมุมเท่ากับ phase ของสัญญาณ

ตัวอย่างที่ $A=1,\phi=30^\circ$: $I=0.8660, Q=0.5, x=0.8660+j0.5$

**ความแตกต่างที่ควรจำ:** I/Q ชุดเดียวระบุตำแหน่งเวกเตอร์ ณ เวลาเดียว แต่ FM ต้องรู้ว่าเวกเตอร์หมุนจากตำแหน่งก่อนหน้าไปปัจจุบันอย่างไร

## 6. ทำไมต้องใช้สอง samples ไม่ใช่ I/Q เพียง sample เดียว

หลัง sampling ได้:

$$
x[n]=I[n]+jQ[n]=A_ne^{j\phi_n}
$$

และ

$$
x[n-1]=I[n-1]+jQ[n-1]=A_{n-1}e^{j\phi_{n-1}}
$$

ความต่างเฟสที่ต้องการคือ

$$
\boxed{\Delta\phi[n]=\phi_n-\phi_{n-1}}
$$

วิธีตรงไปตรงมาคือหา `atan2(Q,I)` สองครั้งแล้วลบ **พร้อม wrap กลับเข้าสู่ช่วง ±π** แต่มีทางเลือกที่ใช้การหา argument ของ complex product ครั้งเดียว

ตัวอย่าง phase ก่อนหน้า $179^\circ$ และปัจจุบัน $-179^\circ$: การลบตัวเลขตรง ๆ ให้ $-358^\circ$ แต่ผลต่างบนวงกลม principal angle เท่ากับ $+2^\circ$ (โดยมีข้อสมมติว่าการหมุนจริงไม่ข้ามหลายรอบระหว่าง samples)

## 7. ที่มาของ R และ J จาก complex conjugate multiplication

### 7.1 ทำไมต้อง conjugate ของ sample ก่อนหน้า?

การคูณจำนวนเชิงซ้อนในรูป polar ทำให้เฟสบวกกัน:

$$
e^{j\alpha}e^{j\beta}=e^{j(\alpha+\beta)}
$$

เมื่อ conjugate $x^*[n-1]=A_{n-1}e^{-j\phi_{n-1}}$ เครื่องหมาย phase เดิมกลับด้าน จึงได้:

$$
\begin{aligned}
p[n]&=x[n]x^*[n-1]\\
&=A_ne^{j\phi_n}A_{n-1}e^{-j\phi_{n-1}}\\
&=A_nA_{n-1}e^{j(\phi_n-\phi_{n-1})}\\
&=A_nA_{n-1}e^{j\Delta\phi[n]}
\end{aligned}
$$

นี่คือเหตุผลหลักของการคูณกับ complex conjugate: **นำ phase ปัจจุบันลบ phase ก่อนหน้าโดยไม่ต้องแยกคำนวณทั้งสองมุม**

### 7.2 R และ J ปรากฏจาก Euler อย่างไร?

$$
\begin{aligned}
p[n]&=A_nA_{n-1}[\cos\Delta\phi+j\sin\Delta\phi]\\
&=R[n]+jJ[n]
\end{aligned}
$$

เทียบส่วนจริงและจินตภาพ:

$$
\boxed{R[n]=A_nA_{n-1}\cos\Delta\phi[n]}
$$

$$
\boxed{J[n]=A_nA_{n-1}\sin\Delta\phi[n]}
$$

> `R` และ `J` เป็น **ชื่อตัวแปร** สำหรับ real/imaginary parts ของ $p[n]$ ส่วน `j` ตัวเล็กเป็น imaginary unit. ชื่อ `J` ไม่ใช่มาตรฐานบังคับ; บางงานใช้ `Re`, `Im`, `cross`, `dot` หรือ `z_re`, `z_im` แทน

### 7.3 กระจายการคูณในรูป Cartesian / I-Q

กำหนด shorthand $I_n=I[n], Q_n=Q[n], I_p=I[n-1], Q_p=Q[n-1]$:

$$
\begin{aligned}
p[n] &=(I_n+jQ_n)(I_p-jQ_p)\\
&=I_nI_p-jI_nQ_p+jQ_nI_p-j^2Q_nQ_p\\
&=(I_nI_p+Q_nQ_p)+j(Q_nI_p-I_nQ_p)
\end{aligned}
$$

เพราะ $j^2=-1$ จึงได้สูตรที่ใช้เขียน RTL:

$$
\boxed{R[n]=I_nI_p+Q_nQ_p}
$$

$$
\boxed{J[n]=Q_nI_p-I_nQ_p}
$$

### 7.4 R/J ไม่ใช่ I/Q ใหม่จาก RF chain

- $I[n],Q[n]$: พิกัดของ **sample ปัจจุบัน** เทียบกับแกนอ้างอิงของ baseband
- $R[n],J[n]$: พิกัดของ **product** ระหว่าง sample ปัจจุบันกับ sample ก่อนหน้า
- มุมของ $I+jQ$: absolute/baseband phase ณ sample นั้น
- มุมของ $R+jJ$: relative phase หรือ phase change ระหว่าง samples

## 8. ที่มาทางเรขาคณิต: dot product และ cross product

ให้เวกเตอร์บนระนาบ I/Q:

$$
\mathbf v_p=\begin{bmatrix}I_p\\Q_p\end{bmatrix},\qquad
\mathbf v_n=\begin{bmatrix}I_n\\Q_n\end{bmatrix}
$$

### 8.1 R คือ Dot Product

$$
\mathbf v_p\cdot\mathbf v_n=I_pI_n+Q_pQ_n
$$

ดังนั้น

$$
\boxed{R=\mathbf v_p\cdot\mathbf v_n=A_pA_n\cos\Delta\phi}
$$

ความหมาย: บอกว่าเวกเตอร์มีทิศทางสัมพันธ์กันอย่างไร

- $\Delta\phi=0^\circ$: $R=+A_pA_n$ (ชี้ทิศทางเดียวกัน)
- $\Delta\phi=90^\circ$: $R=0$ (ตั้งฉาก)
- $\Delta\phi=180^\circ$: $R=-A_pA_n$ (ชี้ตรงข้าม)
- R อย่างเดียวไม่รู้ทิศหมุน เพราะ $\cos(+30^\circ)=\cos(-30^\circ)$

### 8.2 J คือ 2D Signed Cross Product / Determinant

$$
\det(\mathbf v_p,\mathbf v_n)=I_pQ_n-Q_pI_n
$$

ดังนั้น

$$
\boxed{J=\det(\mathbf v_p,\mathbf v_n)=A_pA_n\sin\Delta\phi}
$$

ความหมาย: signed area ของรูปสี่เหลี่ยมด้านขนานจากเวกเตอร์ทั้งสอง; เครื่องหมายช่วยบอกทิศหมุนตาม convention ที่ระบุ

- $\Delta\phi=+30^\circ$, $A_p=A_n=1$: $J=+0.5$
- $\Delta\phi=-30^\circ$, $A_p=A_n=1$: $J=-0.5$
- J อย่างเดียวไม่รู้มุมครบทุก quadrant เพราะ $\sin30^\circ=\sin150^\circ$

**สรุปเชิงเรขาคณิต:** R บอกองค์ประกอบ cosine ของมุมระหว่างเวกเตอร์ ส่วน J บอกองค์ประกอบ sine แบบมีเครื่องหมาย ทั้งคู่จึงต้องทำงานร่วมกันหากต้องการมุมเต็มช่วง

## 9. ทำไมต้องใช้ atan2(J,R) แทน atan(J/R)

จาก

$$
R=A_pA_n\cos\Delta\phi,\quad J=A_pA_n\sin\Delta\phi
$$

ถ้า $R\neq0$ จะได้ $J/R=\tan\Delta\phi$ แต่ `atan(J/R)` ทั่วไปไม่รู้ quadrant ที่แท้จริง และใช้ไม่ได้เมื่อ R = 0 จึงใช้:

$$
\boxed{\Delta\phi_{\rm principal}[n]=\operatorname{atan2}(J[n],R[n])}
$$

`atan2(y,x)` ใช้ **อาร์กิวเมนต์แรกเป็นแกน Y = J**, อาร์กิวเมนต์ที่สองเป็นแกน X = R

| $\Delta\phi$ | $R$ ที่ $A_pA_n=1$ | $J$ | `atan2(J,R)` |
|---:|---:|---:|---:|
| +30° | +0.8660 | +0.5000 | +30° |
| +150° | −0.8660 | +0.5000 | +150° |
| −150° | −0.8660 | −0.5000 | −150° |
| −30° | +0.8660 | −0.5000 | −30° |

**ข้อจำกัด:** `atan2` คืน principal angle อยู่ในช่วงประมาณ $[-\pi,+\pi]$ จึงแยกเฟสจริงที่หมุนเกินครึ่งรอบต่อ sample ไม่ได้ (phase aliasing); และเมื่อ $R=J=0$ มุมไม่กำหนด เช่น amplitude ของ sample ใด sample หนึ่งเป็นศูนย์

## 10. พิสูจน์ dφ/dt จาก I/Q โดยตรง

วิธีนี้เป็น Continuous-Time Quadrature Phase Discriminator ซึ่งเกี่ยวข้องกับ J แต่ไม่เหมือนกับ exact discrete `atan2` ในทุกกรณี

เริ่มจาก

$$
I(t)=A(t)\cos\phi(t),\qquad Q(t)=A(t)\sin\phi(t)
$$

กำหนด dot = derivative เทียบเวลา

$$
\dot I=\dot A\cos\phi-A\sin\phi\,\dot\phi
$$

$$
\dot Q=\dot A\sin\phi+A\cos\phi\,\dot\phi
$$

คำนวณ

$$
I\dot Q-Q\dot I
$$

แทนค่าและจัดกลุ่ม:

$$
\begin{aligned}
I\dot Q-Q\dot I
&=A\cos\phi(\dot A\sin\phi+A\cos\phi\,\dot\phi)\\
&\quad-A\sin\phi(\dot A\cos\phi-A\sin\phi\,\dot\phi)\\
&=A\dot A\cos\phi\sin\phi+A^2\cos^2\phi\,\dot\phi\\
&\quad-A\dot A\sin\phi\cos\phi+A^2\sin^2\phi\,\dot\phi\\
&=A^2(\cos^2\phi+\sin^2\phi)\dot\phi\\
&=A^2\dot\phi
\end{aligned}
$$

เพราะ $I^2+Q^2=A^2$ จึงได้

$$
\boxed{\frac{d\phi}{dt}=\frac{I\,dQ/dt-Q\,dI/dt}{I^2+Q^2}}
$$

**บทบาทของตัวหาร:** หาร magnitude squared เพื่อชดเชย amplitude ให้ตัวเศษที่มี factor $A^2$ กลายเป็น derivative ของ phase อย่างเดียว; ความสัมพันธ์นี้ถูกต้องสำหรับสัญญาณเรียบและ magnitude ไม่เป็นศูนย์ แม้ amplitude เปลี่ยนตามเวลา

วิธีพิสูจน์อีกแบบคือเริ่มจาก $\phi=\operatorname{atan2}(Q,I)$ แล้วใช้ chain rule (ในบริเวณที่ต่อเนื่อง) จะได้สมการเดียวกัน

## 11. ทำไม J-only ใช้ประมาณ derivative ได้?

### 11.1 Discretize อนุพันธ์ด้วย finite difference

สำหรับ sample ห่างกัน $T_s$:

$$
\dot I(t_n)\approx\frac{I[n]-I[n-1]}{T_s}
$$

$$
\dot Q(t_n)\approx\frac{Q[n]-Q[n-1]}{T_s}
$$

นำไปแทนในตัวเศษ $I\dot Q-Q\dot I$:

$$
\begin{aligned}
I_n\dot Q-Q_n\dot I
&\approx\frac{I_n(Q_n-Q_p)-Q_n(I_n-I_p)}{T_s}\\
&=\frac{I_nQ_n-I_nQ_p-Q_nI_n+Q_nI_p}{T_s}\\
&=\frac{Q_nI_p-I_nQ_p}{T_s}\\
&=\frac{J[n]}{T_s}
\end{aligned}
$$

ดังนั้น finite-difference derivative estimator เป็น

$$
\boxed{\dot\phi(t_n)\approx\frac{J[n]}{T_s\,[I[n]^2+Q[n]^2]}}
$$

**สำคัญ:** นี่คือ backward-difference approximation ไม่ใช่เอกลักษณ์ทางคณิตศาสตร์แบบ exact สำหรับ samples ที่มี phase increment มากหรือ amplitude เปลี่ยนเร็ว

### 11.2 ทำไม J จึงใกล้เคียง phase increment?

จากสูตรที่ exact สำหรับผลคูณ complex:

$$
\boxed{J[n]=A_nA_p\sin\Delta\phi[n]}
$$

เมื่อ $|\Delta\phi|\ll1$ rad:

$$
\sin\Delta\phi\approx\Delta\phi
$$

ดังนั้น

$$
J[n]\approx A_nA_p\,\Delta\phi[n]
$$

ถ้า amplitude ประมาณ 1 และคงที่:

$$
\boxed{J[n]\approx\Delta\phi[n]}
$$

จากนั้น

$$
\boxed{f_{\rm bb}[n]\approx\frac{f_s}{2\pi}J[n]}
$$

เฉพาะกรณีที่ $A_nA_p\approx1$; มิฉะนั้นต้องชดเชย gain/amplitude ตามแบบจำลองที่เลือก

### 11.3 ตัวเลือกการออกแบบและความเที่ยงตรง

| วิธี | ค่าที่ใช้ | ข้อดี | ข้อจำกัด |
|---|---|---|---|
| Full polar | `atan2(J,R)` | Phase increment หลักได้โดยตรง; ไม่ไวต่อ amplitude scaling ที่เป็นบวก | ต้องมี angle calculation; phase aliasing ยังคงมี |
| J-only | `J` | ใช้ 2 multiply + 1 subtract (ก่อน scaling) | ติด factor $A_nA_p$ และ nonlinear $\sin\Delta\phi$ |
| Magnitude-normalized J | $J/(A_nA_p)$ | ได้ $\sin\Delta\phi$ โดยลด amplitude factor | ต้องหา magnitude/หาร; ยังไม่ใช่ phase increment |
| Approx derivative | $J/[T_s(I_n^2+Q_n^2)]$ | เชื่อมกับ continuous derivative | Finite difference error และความไวเมื่อ amplitude ต่ำ |

**ตัวอย่าง ambiguity:** $J=0.5$ เกิดได้ทั้ง $30^\circ$ และ $150^\circ$ เมื่อ amplitude คงที่ 1 แต่ R เป็นบวกในกรณีแรกและลบในกรณีหลัง

## 12. ตัวอย่างคำนวณทีละขั้น

### 12.1 ตัวอย่างเชิงเรขาคณิตจาก I/Q จำนวนเต็ม

ให้

$$
x[n-1]=3+j4,\qquad x[n]=-4+j3
$$

คำนวณ:

$$
R=(-4)(3)+(3)(4)=0
$$

$$
J=(3)(3)-(-4)(4)=25
$$

ดังนั้น

$$
\Delta\phi=\operatorname{atan2}(25,0)=\frac{\pi}{2}=90^\circ
$$

ตรวจคำตอบจาก absolute phase: ก่อนหน้า $\operatorname{atan2}(4,3)=53.13^\circ$ และปัจจุบัน $\operatorname{atan2}(3,-4)=143.13^\circ$ ต่างกัน $90^\circ$

### 12.2 ตัวอย่างเชิงความถี่: 400 kS/s และ +5 kHz

**สมมติสำหรับสาธิต** complex baseband magnitude = 1, sampling rate ที่เข้า discriminator $f_s=400{,}000$ samples/s, ความถี่เบี่ยงเบน ณ ช่วงนั้น $f=+5{,}000$ Hz

$$
\Delta\phi=2\pi\frac{f}{f_s}=2\pi\frac{5000}{400000}=0.078539816\ \text{rad}=4.5^\circ
$$

จึงได้

$$
R=\cos(0.078539816)=0.996917334
$$

$$
J=\sin(0.078539816)=0.078459096
$$

**Full R/J:**

$$
\widehat f=\frac{f_s}{2\pi}\operatorname{atan2}(J,R)=5000\ \text{Hz}
$$

**J-only normalized approximation:**

$$
\widehat f_J\approx\frac{400000}{2\pi}(0.078459096)=4994.861\ \text{Hz}
$$

J-only ต่ำกว่าค่าจริงประมาณ **5.139 Hz หรือ 0.103%** ในตัวอย่างนี้ (เพราะ $\sin\theta<\theta$ เมื่อ $\theta>0$ ขนาดเล็ก)

### 12.3 Phase difference ที่ใหญ่ขึ้น

เมื่อ magnitude = 1:

| Phase difference จริง | `atan2(J,R)` | J ตีความเป็น rad แล้วแปลงเป็นองศา |
|---:|---:|---:|
| 10° | 10° | 9.95° |
| 30° | 30° | 28.65° |
| 60° | 60° | 49.62° |
| 90° | 90° | 57.30° |
| 150° | 150° | 28.65° |

ยิ่ง phase increment สูง การแทน $\sin\theta$ ด้วย $\theta$ ยิ่งมี distortion มาก โดยที่มุม 150° J-only แยกจาก 30° ไม่ได้

### 12.4 ข้อจำกัด sampling

Phase increment แบบไม่มี alias ของ polar method ต้องอยู่ในช่วงหลัก เช่น

$$
|\Delta\phi[n]|<\pi
\quad\Longleftrightarrow\quad
|f_{\rm bb}|<f_s/2
$$

เมื่อความถี่คงที่ในช่วง sample และไม่มี spectral aliasing อื่น ๆ ทั้งนี้ **การเลือก $f_s$ เพียงแค่ทำให้ไม่ alias ไม่ได้แปลว่า J-only จะ linear ดี**; ควรเลือกให้ $2\pi|f|/f_s$ มีค่าน้อยเพียงพอต่อ error budget ด้วย

## 13. วิเคราะห์บทความ IF 10.7 MHz [1] แบบแยก source กับข้อสรุป

### 13.1 ข้อมูลที่บทความระบุ

บทความอธิบาย receiver บน Spartan-7 FPGA โดยใช้องค์ประกอบ:

```text
10.7 MHz FM IF
     |
ADC at 10 MS/s (bandpass undersampling)
     |
Aliased center frequency ~ 0.7 MHz
     |
Quadrature digital mixing (DDS cos / -sin)
     |
I/Q FIR filtering and decimation
     |
Discrete FM discriminator
     |
Recovered message / output path
```

ความถี่ alias สำหรับ center frequency นี้:

$$
f_{\rm alias}=|10.7\ \text{MHz}-1\times10\ \text{MHz}|=0.7\ \text{MHz}
$$

บทความระบุ FIR/decimation ชั้นแรก factor 5: 10 MS/s → 2 MS/s และชั้นที่สองอีก factor 5: 2 MS/s → 400 kS/s [1]

### 13.2 สมการ discriminator ที่ระบุในบทความ

$$
\boxed{y[n]=I[n](Q[n]-Q[n-1])-Q[n](I[n]-I[n-1])}
$$

เมื่อกระจายและตัด $I_nQ_n-Q_nI_n$:

$$
\boxed{y[n]=Q_nI_p-I_nQ_p=J[n]}
$$

ดังนั้น **สมการในบทความเป็น J-only cross-product discriminator** ไม่ใช่ full polar discriminator ที่ต้องหา R และ `atan2(J,R)`

### 13.3 จุดที่ต้องอ่าน VHDL อย่างระมัดระวัง

- บทความบอก output rate หลัง FIR สองชั้น 400 kS/s; **อัตรานี้ไม่ใช่โดยอัตโนมัติว่า discriminator ทำงานที่ 400 kS/s**
- ใน excerpt top-level VHDL ที่เผยแพร่ มีตัวอย่างการ instantiation `FM_Demodulation` ซึ่งต่อ `IMixed_After_FIR` / `QMixed_After_FIR` ของ FIR ชั้นแรก **แต่โค้ดส่วน instantiation ที่มองเห็นถูก comment out** [1]
- จึงไม่ควรสรุปว่าเส้นทางนั้นคือ active data path; ต้องดู RTL ที่ compile จริง, การต่อ valid และ timing waveform เพื่อยืนยัน sample rate ณ discriminator
- คำอธิบายที่ว่า robust to amplitude variations ในบทความไม่ควรถูกตีความว่า J-only **invariant** ต่อ amplitude เพราะจาก $J=A_nA_p\sin\Delta\phi$ ยังมี amplitude factor อยู่

## 14. ถ้าจะออกแบบเป็น RTL บน FPGA / ASIC

### 14.1 Block diagram แบบ Full R/J

```text
I[n] ---------+----------------------+
               |                      |
Q[n] ---------+-> register previous  |
               |                      |
I_prev -------+--  R = I*I_prev + Q*Q_prev
Q_prev -------+--  J = Q*I_prev - I*Q_prev
                                  |
                           atan2(J, R)
                            (CORDIC)
                                  |
                      frequency scaling
                                  |
                          recovered FM
```

สำหรับ **J-only** ใช้เพียง `Q*I_prev - I*Q_prev` แล้วกำหนด gain / optional amplitude conditioning ก่อนส่งออก

### 14.2 ทรัพยากรคำนวณในแบบตรงไปตรงมา

| Datapath | Real multipliers | Arithmetic เพิ่มเติม |
|---|---:|---|
| J-only | 2 | 1 subtract |
| R/J product | 4 | 1 add + 1 subtract |
| atan2 | — | CORDIC/vectoring หรือ implementation อื่น |

ตัวเลขนี้เป็นระดับ **arithmetic expressions** ไม่ใช่จำนวน DSP slices สุดท้าย เพราะ synthesis, pipelining, resource sharing, FPGA architecture และ throughput requirements ส่งผลต่อ mapping จริง

### 14.3 Fixed-point sizing ตัวอย่าง

สมมติ I และ Q เป็น signed 16-bit (`Q1.15`) แต่ละ product ที่คำนวณจากสอง samples ต้องเผื่อผลคูณกว้าง **32 บิต** ก่อนทำการบวก/ลบ จากนั้น `R` และ `J` ควรเผื่อ carry เพิ่มอีกอย่างน้อย **1 บิต** (เช่น signed 33-bit ก่อน scaling/rounding/saturation) เพื่อลด overflow

รายละเอียด binary point ของผลคูณ `Q1.15 × Q1.15` สัมพันธ์กับ 30 fractional bits (`Q2.30` ตาม convention ที่ใช้) ต้องจัด shift/round และ saturation ให้ถูกต้องก่อนลด word length

### 14.4 Sample valid สำคัญกว่านาฬิกา logic

ถ้า FPGA clock 200 MHz แต่ตัวอย่าง I/Q ใหม่เข้ามาทุก 100 clock จะมี sample rate 2 MS/s ไม่ใช่ 200 MS/s

**Previous-sample register ต้องอัปเดตเฉพาะตอน sample handshake สำเร็จ** เช่น `i_valid && o_ready` หรือสัญญาณ enable ที่หมายถึง sample ใหม่จริง ๆ มิฉะนั้น J จะคำนวณกับข้อมูลเดิมซ้ำและผิดความหมาย

### 14.5 Design / Verification checklist

1. กำหนดสัญญาณ input เป็น complex baseband และตรวจ polarity ของ Q
2. กำหนด $f_s$ หลัง decimation ให้ตรงกับ sample ที่ discriminator รับ
3. กำหนดขอบเขต $|\Delta\phi|$ และ error budget ของ J-only
4. สร้าง floating-point golden model: `arg(x[n] * conj(x[n-1]))`
5. สร้าง golden model J-only: `imag(x[n] * conj(x[n-1]))`
6. ทดสอบ tone ที่ +f, −f, 0 และ phase wrap ผ่าน ±π
7. ทดสอบ amplitude scale เช่น 0.5×, 1×, 2× เพื่อดู gain effect
8. ทดสอบ amplitude zero/near-zero, noise, clipping และ overflow
9. ทดสอบ valid gaps, stalls และ reset ว่า previous-sample semantics ถูกต้อง
10. วัด MSE/EVM หรือ frequency estimation error, latency, fMAX, LUT/FF/DSP/BRAM

## 15. จุดที่ผิดพลาดกันบ่อย

**(ก) นึกว่า $x(t)=A\cos\phi+jA\sin\phi$ เป็นแรงดัน RF จริง:** ไม่ใช่; เป็น complex representation หรือ complex baseband ตามนิยาม ส่วน waveform analog จริงเป็น real

**(ข) คิดว่า R และ J เป็นช่องสัญญาณที่ได้จาก ADC เพิ่ม:** ไม่ใช่; คำนวณขึ้นจาก I/Q สอง samples

**(ค) คิดว่า R เท่ากับ magnitude:** ไม่ใช่; R คือ dot product และอาจติดลบ ส่วน magnitude ของ product คือ $\sqrt{R^2+J^2}=A_nA_p$

**(ง) คิดว่า J เป็น phase เสมอ:** ไม่ใช่; J เท่ากับ magnitude product คูณ sine ของ phase difference; จะใกล้กับ phase ก็ต่อเมื่อมี normalization และ small-angle assumption

**(จ) คิดว่า atan2 ป้องกัน phase alias ได้ทั้งหมด:** ไม่ใช่; atan2 ให้ principal angle เท่านั้น หาก phase เพิ่ม > π ในหนึ่ง sample ก็ยังแยกจำนวนรอบไม่ได้

**(ฉ) ใช้ FPGA logic clock เป็น $f_s$ ในสมการ:** ไม่ถูก หาก sampling ถูก decimate/valid gated ต้องใช้ sample rate ณ block

**(ช) คิดว่า derivative formula กับ discrete atan2 เหมือนกันเสมอ:** ไม่เหมือน; continuous derivative เป็นค่า ณ ขณะหนึ่ง, finite difference เป็น approximation และ `atan2` ให้ phase increment หลักระหว่างสอง samples

**(ซ) ลืม DC frequency offset และสัญญาณหลัง demod:** ถ้า LO ไม่ตรงศูนย์กลาง carrier, discriminator อาจมี DC offset ต้องชดเชย และสำหรับ FM audio อาจต้องมี de-emphasis / audio low-pass / resampling ตามระบบปลายทาง

## 16. Summary: สมการสำคัญและภาพรวมหนึ่งหน้า

**FM definition**

$$
f_i(t)=f_c+k_fm(t)
$$

**Phase derivative**

$$
f_i(t)=\frac{1}{2\pi}\frac{d\phi_{\rm RF}}{dt}
$$

**Complex baseband (ไม่ใช่ real RF waveform)**

$$
x_{\rm BB}(t)=I(t)+jQ(t)=A(t)e^{j\theta(t)}
$$

**Continuous I/Q discriminator**

$$
\frac{d\theta}{dt}=\frac{I\dot Q-Q\dot I}{I^2+Q^2}
$$

**Discrete complex product**

$$
p[n]=x[n]x^*[n-1]=R[n]+jJ[n]
$$

$$
R[n]=I_nI_p+Q_nQ_p=A_nA_p\cos\Delta\theta[n]
$$

$$
J[n]=Q_nI_p-I_nQ_p=A_nA_p\sin\Delta\theta[n]
$$

**Full polar discriminator**

$$
\boxed{\widehat f_{\rm bb}[n]=\frac{f_s}{2\pi}\operatorname{atan2}(J[n],R[n])}
$$

**J-only approximation เมื่อ amplitude คงที่และ phase increment เล็ก**

$$
\widehat f_{\rm bb}[n]\approx\frac{f_s}{2\pi}\frac{J[n]}{A_nA_p}
$$

หาก magnitude normalize เป็น 1 จะเหลือ $f_sJ/(2\pi)$ โดย approximation นี้มี nonlinear error ตาม $\sin\Delta\theta$

**ใจความที่ควรจำ:** `I/Q` บอกเวกเตอร์อยู่ตรงไหน; `R/J` บอกเวกเตอร์หมุนเปลี่ยนไปเท่าไร; `d(phase)/dt` บอกว่าหมุนเร็วแค่ไหน; ความเร็วการหมุนคือ instantaneous frequency ที่ใช้กู้ข้อมูล FM

---

## แหล่งอ้างอิง

**[1] Embedded Design.** “FM Demodulation of a 10.7MHz IF Frequency.” บทความโครงสร้าง Spartan-7, undersampling, DDC, FIR และสมการ J-only.  
https://embeddeddesign.org/fm-demodulation-of-a-10-7mhz-if-frequency/

**[2] GNU Radio Manual / C++ API.** `gr::analog::quadrature_demod_cf` — หลักการ $\arg(x[n]\overline{x[n-1]})$ สำหรับ FM/FSK/GMSK.  
https://www.gnuradio.org/doc/doxygen/classgr_1_1analog_1_1quadrature__demod__cf.html

**[3] Wireless Pi.** “Frequency Modulation (FM) and Demodulation Using DSP Techniques.” อ่านเพิ่มเติมเรื่อง phase/frequency และ DSP demodulation.  
https://wirelesspi.com/frequency-modulation-fm-and-demodulation-using-dsp-techniques/

**[4] GNU Radio Wiki.** “Quadrature Demod” — เอกสารเพิ่มเติมเรื่อง quadrature demodulation และ gain.  
https://wiki.gnuradio.org/index.php/Quadrature_Demod

**สถานะหลักฐาน:** [1] และ [2] ใช้ยืนยันรายละเอียดบทความและ discriminator ที่ตรวจสอบได้; นิพจน์ dot/cross product, Euler, differentiation, small-angle error และตัวอย่างตัวเลขเป็นการพิสูจน์หรือคำนวณทางคณิตศาสตร์ที่แสดงขั้นตอนครบในเอกสารนี้ ไม่ใช่การอ้างว่าเป็นประโยคในบทความต้นทาง
