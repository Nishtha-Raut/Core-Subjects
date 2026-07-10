# 04: Data Transmission Basics

## 1. Analog vs Digital Signals

| Aspect | Analog Signal | Digital Signal |
|---|---|---|
| Nature | Continuous, smoothly varying waveform | Discrete values (typically 0s and 1s) |
| Representation | Sine waves, infinite possible values | Binary levels — finite, discrete set of values |
| Noise resistance | More susceptible to noise/distortion over distance | More resistant to noise (easier to distinguish discrete levels) |
| Example | Traditional telephone voice signal, radio waves | Computer data, digital audio (CDs, MP3) |

```
Analog Signal (continuous):          Digital Signal (discrete):

  /\    /\    /\                      __    __    __
 /  \  /  \  /  \                    |  |  |  |  |  |
/    \/    \/    \                ___|  |__|  |__|  |___
   (smooth sine wave)              (square wave, 0s and 1s)
```

## 2. Line Coding (Digital-to-Digital Encoding)
- **Line Coding** converts a sequence of bits into a digital signal pattern for transmission over a physical medium.

### a. NRZ (Non-Return to Zero)
- **NRZ-L (Level):** 1 = high voltage, 0 = low voltage (or vice versa). Simple, but has a **synchronization problem** with long runs of consecutive 0s or 1s (no transitions to help the receiver's clock stay in sync).
- **NRZ-I (Invert on ones):** A transition (change in voltage level) represents a **1**; no transition represents a **0**. Still has sync issues with long runs of 0s.

### b. Manchester Encoding
- A **transition occurs in the middle of every bit** — low-to-high transition = one bit value (e.g., 0), high-to-low = the other (e.g., 1) — convention varies.
- **Self-clocking:** the guaranteed mid-bit transition means the receiver can always recover the clock/timing from the signal itself, solving NRZ's synchronization problem.
- **Trade-off:** Requires **twice the bandwidth** of NRZ, since there are more transitions per bit. Used in classic Ethernet (10BASE-T).

```
Bit sequence:      1     0     1     1     0
NRZ-L:          ___     _____       _____
                    |___|     |_____|     |___
                  (1=high, 0=low, no forced transitions)

Manchester:      _|‾   ‾|_    _|‾    _|‾   ‾|_
                (transition in the MIDDLE of every bit — self-clocking)
```

## 3. Modulation (Digital-to-Analog Encoding)
- Used when digital data must be transmitted over an **analog** medium (e.g., traditional telephone lines) — this is exactly what a **Modem** does (Modulator-Demodulator).

| Technique | Full Form | How it Works |
|---|---|---|
| **ASK** | Amplitude Shift Keying | Varies the **amplitude** of the carrier signal to represent bits (e.g., high amplitude = 1, low/no amplitude = 0). Simple, but very susceptible to noise. |
| **FSK** | Frequency Shift Keying | Varies the **frequency** of the carrier signal to represent bits (e.g., one frequency = 1, another = 0). More noise-resistant than ASK. |
| **PSK** | Phase Shift Keying | Varies the **phase** of the carrier signal to represent bits. More efficient and noise-resistant; used in modern WiFi/modem standards. |
| **QAM** | Quadrature Amplitude Modulation | Combines both amplitude AND phase variations to encode **multiple bits per symbol**, achieving higher data rates. Used in cable modems, WiFi, digital TV. |

## 4. Nyquist Theorem & Shannon's Theorem (Channel Capacity)

### Nyquist Theorem (Noiseless Channel)
- Determines the **maximum data rate** for a **noiseless** channel, given its bandwidth.
- **Formula:** `Max Data Rate = 2 × B × log2(L)` bits/sec
  - `B` = Bandwidth of the channel (Hz)
  - `L` = Number of discrete signal levels used to represent data

### Shannon's Theorem (Noisy Channel)
- Determines the **theoretical maximum data rate** for a **noisy** channel, given its bandwidth and Signal-to-Noise Ratio (SNR).
- **Formula:** `Capacity (C) = B × log2(1 + SNR)` bits/sec
  - `B` = Bandwidth of the channel (Hz)
  - `SNR` = Signal-to-Noise Ratio (often given in dB, needs conversion: `SNR = 10^(SNR_dB/10)`)

> **Extra — Worked example (common numerical interview question):**
> Given a channel with Bandwidth = 3000 Hz and SNR = 30 dB, find the channel capacity using Shannon's theorem.
> 1. Convert SNR from dB to a ratio: `SNR = 10^(30/10) = 10^3 = 1000`
> 2. Apply Shannon's formula: `C = 3000 × log2(1 + 1000) = 3000 × log2(1001) ≈ 3000 × 9.97 ≈ 29,910 bits/sec ≈ 29.9 kbps`

> **Extra — Nyquist vs Shannon comparison:**

| Aspect | Nyquist Theorem | Shannon's Theorem |
|---|---|---|
| Applies to | Noiseless channels | Noisy channels (realistic scenario) |
| Key variable | Number of signal levels (L) | Signal-to-Noise Ratio (SNR) |
| Practical relevance | Theoretical upper bound (idealized) | More realistic estimate for real-world channels |
| Relationship | Increasing L increases data rate (but has practical limits due to noise) | Sets the true ceiling — even with infinite signal levels, noise limits actual achievable capacity |

## 5. Multiplexing
- **Multiplexing** is the technique of combining **multiple signals into one** for transmission over a shared medium, then separating them at the receiving end (**demultiplexing**).
- Purpose: efficiently utilize the available bandwidth of a communication channel.

### a. FDM (Frequency Division Multiplexing)
- Divides the available **bandwidth into multiple frequency bands**, each carrying a separate signal simultaneously.
- Used in: traditional radio/TV broadcasting, older analog telephone systems.

```
Frequency ->
|--Signal A (band 1)--|--Signal B (band 2)--|--Signal C (band 3)--|
     (all transmitted simultaneously, on different frequency ranges)
```

### b. TDM (Time Division Multiplexing)
- Divides transmission **time into slots**, and each signal gets to use the **full bandwidth** during its own assigned time slot, in rotation.
- **Synchronous TDM:** Fixed time slots assigned to each source, regardless of whether that source has data to send (can waste slots if a source is idle).
- **Statistical (Asynchronous) TDM:** Time slots allocated **dynamically**, only to sources that actually have data to send — more efficient use of bandwidth.

```
Time ->
|--A--|--B--|--C--|--A--|--B--|--C--|--A--|--B--|--C--|
  (each signal takes turns using the FULL bandwidth, for its time slot)
```

### c. WDM (Wavelength Division Multiplexing)
- A form of FDM used specifically for **fiber-optic** communication — different signals are transmitted simultaneously using **different wavelengths (colors) of light** over the same fiber.
- **DWDM (Dense WDM):** Packs many more wavelengths closer together, used in high-capacity, long-haul fiber backbones.

> **Extra — FDM vs TDM comparison:**

| Aspect | FDM | TDM |
|---|---|---|
| Division basis | Frequency (shared simultaneously, different bands) | Time (shared sequentially, full bandwidth per slot) |
| Guard requirement | Needs "guard bands" between frequencies to avoid interference | Needs "guard time" between slots to avoid overlap |
| Common use | Analog broadcasting, satellite | Digital telephony, computer networks |

---

## Interview Questions & Answers

**Q1. What is the fundamental difference between Analog and Digital signals?**
Analog signals are continuous waveforms that can take any value within a range (e.g., a smooth sine wave), while Digital signals use discrete, finite values (typically binary 0s and 1s). Digital signals are generally more resistant to noise, since it's easier for a receiver to distinguish between a small number of discrete levels than to precisely reconstruct a continuously varying analog waveform after it's been degraded by noise.

**Q2. What problem does Manchester Encoding solve compared to simple NRZ encoding, and what's the trade-off?**
NRZ encoding has a synchronization problem — long runs of consecutive identical bits (all 0s or all 1s) produce no voltage transitions, making it hard for the receiver to stay synchronized with the sender's clock. Manchester Encoding solves this by guaranteeing a signal transition in the middle of EVERY bit, making it self-clocking. The trade-off is that Manchester encoding requires roughly twice the bandwidth of NRZ, since it has more frequent transitions per bit transmitted.

**Q3. What is Modulation, and why is it needed?**
Modulation is the process of converting digital data into an analog signal suitable for transmission over an analog medium (like traditional telephone lines), by varying a property (amplitude, frequency, or phase) of a carrier wave. It's needed because many transmission media (especially legacy telephone infrastructure) were designed for analog voice signals, so digital data must be "translated" into an analog-compatible form to be transmitted over them — this is exactly what a Modem (MOdulator-DEModulator) does.

**Q4. What is the difference between ASK, FSK, and PSK modulation techniques?**
ASK (Amplitude Shift Keying) varies the amplitude of the carrier signal to represent bits, but is highly susceptible to noise. FSK (Frequency Shift Keying) varies the frequency instead, offering better noise resistance than ASK. PSK (Phase Shift Keying) varies the phase of the signal, offering even better efficiency and noise resistance, and is commonly used in modern communication standards.

**Q5. State Nyquist's Theorem and explain what it calculates.**
Nyquist's Theorem calculates the maximum theoretical data rate achievable over a NOISELESS channel, given its bandwidth (B) and the number of discrete signal levels used (L), using the formula: Max Data Rate = 2 × B × log2(L). It represents an idealized upper bound, assuming there's no noise interfering with the signal.

**Q6. State Shannon's Theorem and explain how it differs from Nyquist's Theorem.**
Shannon's Theorem calculates the maximum theoretical channel capacity for a NOISY channel (which is the realistic, real-world scenario), given its bandwidth (B) and Signal-to-Noise Ratio (SNR), using the formula: Capacity = B × log2(1 + SNR). Unlike Nyquist's Theorem (which assumes a noiseless channel and depends on the number of signal levels used), Shannon's Theorem accounts for real-world noise and sets a true, practical ceiling on achievable data rate — even using an infinite number of signal levels, noise ultimately limits the actual maximum capacity.

**Q7. What is Multiplexing, and why is it used?**
Multiplexing is the technique of combining multiple individual signals into a single combined signal for transmission over a shared communication medium, in order to efficiently utilize the available bandwidth of that medium — rather than requiring a separate dedicated physical channel for every individual signal/connection.

**Q8. What is the difference between FDM and TDM?**
FDM (Frequency Division Multiplexing) divides the available bandwidth into multiple separate frequency bands, allowing multiple signals to be transmitted SIMULTANEOUSLY, each on its own frequency band. TDM (Time Division Multiplexing) instead divides transmission TIME into slots, allowing each signal to use the FULL available bandwidth, but only during its assigned time slot, with signals taking turns in rotation.

**Q9. What is the difference between Synchronous TDM and Statistical (Asynchronous) TDM?**
Synchronous TDM assigns fixed time slots to each source in a strict, predetermined rotation, regardless of whether that source actually has data to send at that moment — this can waste bandwidth if a source is idle during its turn. Statistical (Asynchronous) TDM allocates time slots dynamically, only to sources that actually have data ready to transmit at that time, making much more efficient use of the available bandwidth overall.

**Q10. What is WDM, and how does it relate to FDM?**
WDM (Wavelength Division Multiplexing) is essentially a specialized application of FDM's core principle (dividing a shared medium by frequency), but applied specifically to fiber-optic communication — different signals are transmitted simultaneously using different wavelengths (colors) of light over the same optical fiber, allowing massive amounts of data to be carried over a single physical fiber cable simultaneously.

**Q11. If you increase the number of signal levels (L) in Nyquist's formula, does the data rate increase indefinitely? Why or why not, in a practical sense?**
Mathematically, yes — Nyquist's formula shows that increasing L (using more discrete signal levels) increases the theoretical maximum data rate logarithmically. However, in practice, this is limited by real-world noise: as you pack more signal levels closer together (to fit within the same amplitude/frequency range), it becomes increasingly difficult for a receiver to reliably distinguish between adjacent levels in the presence of noise — this is precisely the practical limitation that Shannon's Theorem accounts for, which is why real-world channel capacity is ultimately bounded by SNR, not just by how many signal levels you theoretically choose to use.

**Q12. Why is Manchester Encoding specifically associated with classic (10BASE-T) Ethernet, and is it still commonly used today?**
Manchester Encoding's self-clocking property made it well-suited for early Ethernet standards, where reliable clock synchronization between sender and receiver was a significant practical challenge over shared cabling. However, because Manchester encoding requires double the bandwidth of simpler encoding schemes, modern high-speed Ethernet standards (like Gigabit Ethernet and beyond) have moved to more bandwidth-efficient encoding schemes (like 8b/10b or PAM encoding) that achieve synchronization through other means without sacrificing as much usable bandwidth.
