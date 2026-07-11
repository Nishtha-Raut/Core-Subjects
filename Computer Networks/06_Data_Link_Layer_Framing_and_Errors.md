# 06: Data Link Layer — Framing & Error Detection/Correction

## 1. Framing
- **Framing** is the Data Link layer's process of dividing a stream of bits into manageable units called **Frames**, adding a **header** (source/destination MAC, control info) and **trailer** (error-checking info) around the payload.
- Necessary because the Physical layer only deals with a raw, continuous stream of bits — the Data Link layer needs to mark where one unit of data **starts and ends**.

### Framing Methods
- **Character/Byte Count:** A field in the header specifies the number of bytes/characters in the frame. **Problem:** if this count field itself gets corrupted, the receiver loses synchronization for all subsequent frames too.
- **Character/Byte Stuffing:** Uses special **flag bytes** to mark frame boundaries; if the actual data happens to contain a byte identical to the flag, an **escape character** is inserted before it (stuffed) so the receiver doesn't misinterpret it as a boundary.
- **Bit Stuffing:** Uses a special **bit pattern** (commonly `01111110`) to mark frame boundaries. If the data contains **five consecutive 1s**, the sender automatically **stuffs a 0** after them; the receiver removes this stuffed 0 upon seeing the pattern, recovering the original data.

```
Bit Stuffing Example:
Original data:  0111111 0 1
                    ^^^^^ (five consecutive 1s detected)
After stuffing: 0111110 1 0 1   <- a 0 is inserted after the five 1s
```

## 2. Why Errors Happen
- During transmission, **noise, interference, or attenuation** can cause bits to be **flipped** (a 0 becomes a 1, or vice versa) — this is called a **transmission error**.
- **Single-bit error:** Only one bit in the data unit is altered.
- **Burst error:** Two or more **consecutive** bits are altered (more common in real-world serial transmission, since noise often affects a duration of time, not just a single bit).

## 3. Error Detection Techniques

### a. Parity Check
- Adds a single extra **parity bit** to the data, making the total number of 1s either **even** (Even Parity) or **odd** (Odd Parity).
- **Simple, low overhead**, but can only reliably detect an **odd number of bit errors** — it **fails to detect** an even number of bit flips (e.g., 2 bits flipped cancels out and looks "correct").

```
Data: 1011001 (four 1s — already even)
Even Parity bit added: 0   ->  Transmitted: 10110010 (still four 1s = even, valid)

If two bits flip during transmission (e.g., positions 1 and 3):
Received: 11111010 -- count of 1s is now 6 (still even!) -- ERROR NOT DETECTED
```

### b. Checksum
- Data is divided into fixed-size segments; these segments are **summed** (with carry wraparound), and the **complement** of this sum is sent as the checksum.
- Receiver performs the same sum (including the received checksum) — if the result is **all 1s** (or zero, depending on convention), the data is assumed error-free.
- Used at higher layers too (e.g., in TCP/UDP/IP headers).

### c. CRC (Cyclic Redundancy Check)
- The **most powerful and widely used** error-detection technique in real networks (used in Ethernet, etc.).
- Treats the data as a **binary polynomial**, and divides it by a fixed, predetermined **generator polynomial** using **modulo-2 (XOR) division**. The **remainder** becomes the CRC code, appended to the data.
- Receiver performs the same division on the received data (including the CRC) — if the remainder is **zero**, the data is assumed error-free.

> **Extra — Worked CRC example (common numerical interview question):**
> **Given:** Data = `1101011011`, Generator = `10011` (this is a 4-bit CRC, since generator has 5 bits, so we append 4 zero bits).
>
> 1. Append `(n-1)` zeros to the data, where `n` = length of generator (5 bits) → append 4 zeros: `11010110110000`
> 2. Perform modulo-2 (XOR) division of this by the generator `10011`, keeping only the **remainder**.
> 3. Suppose the remainder (CRC) computed is `1110` (example value — actual computation involves step-by-step XOR division).
> 4. The transmitted data becomes: original data + CRC = `1101011011` + `1110` = `11010110111110`.
> 5. **At the receiver:** divide the entire received data (including CRC) by the same generator `10011`. If the remainder is **0**, no error is detected; if non-zero, an error occurred during transmission.

> **Extra — Parity vs Checksum vs CRC comparison:**

| Technique | Detects | Overhead | Strength |
|---|---|---|---|
| Parity Bit | Single-bit errors (odd number of flips) | Very low (1 bit) | Weakest |
| Checksum | Most burst errors | Low-Medium | Medium |
| CRC | Almost all burst errors (very high accuracy) | Medium | Strongest — industry standard |

## 4. Error Correction Techniques

### Hamming Code
- Unlike detection-only techniques (Parity, CRC), **Hamming Code** can actually **locate and correct single-bit errors**, using multiple parity bits placed at specific positions.
- **Redundant bits** are placed at positions that are **powers of 2** (1, 2, 4, 8, ...); data bits fill the remaining positions.
- **Formula for number of redundant bits (r) needed:** `2^r ≥ m + r + 1`, where `m` = number of data bits.

> **Extra — Worked Hamming Code example (very common numerical interview question):**
> **Given:** 4-bit data = `1011`. Find the number of redundant bits needed, and construct the Hamming code.
>
> 1. `m = 4`. Try `r = 3`: `2^3 = 8 ≥ 4 + 3 + 1 = 8` ✓ — so **3 redundant bits** are needed (r1, r2, r4 at positions 1, 2, 4).
> 2. Total code length = `m + r = 7` bits, positions 1 through 7:
>
> ```
> Position:   1   2   3   4   5   6   7
> Bit:        r1  r2  d1  r4  d2  d3  d4
> Value:      ?   ?   1   ?   0   1   1     <- data bits (1011) placed at positions 3,5,6,7
> ```
>
> 3. **r1** (position 1) checks positions whose binary representation has bit 1 set (1,3,5,7): `r1 XOR d1 XOR d2 XOR d4 = 0` → `r1 = 1 XOR 0 XOR 1 = 0`
> 4. **r2** (position 2) checks positions with bit 2 set (2,3,6,7): `r2 = d1 XOR d3 XOR d4 = 1 XOR 1 XOR 1 = 1`
> 5. **r4** (position 4) checks positions with bit 4 set (4,5,6,7): `r4 = d2 XOR d3 XOR d4 = 0 XOR 1 XOR 1 = 0`
> 6. **Final Hamming code (positions 1–7):** `r1=0, r2=1, d1=1, r4=0, d2=0, d3=1, d4=1` → `0110011`
>
> **At the receiver — error detection/correction:** Recompute each parity check; if all are 0, no error. If any check fails, the **binary sum of the failed check positions gives the exact bit position that is in error**, which can then simply be flipped to correct it — this is the key advantage of Hamming Code over simple detection schemes.

> **Extra — Detection vs Correction (key distinction, common interview question):**

| Aspect | Error Detection (Parity, Checksum, CRC) | Error Correction (Hamming Code) |
|---|---|---|
| Capability | Identifies THAT an error occurred | Identifies WHERE the error occurred, and fixes it |
| Action on error | Request retransmission (e.g., via ARQ) | Can correct without needing retransmission |
| Overhead | Generally lower | Generally higher (more redundant bits needed) |
| Common use | Networking (Ethernet CRC, TCP checksum) | Memory systems (ECC RAM), satellite communication (high retransmission cost) |

---

## Interview Questions & Answers

**Q1. What is Framing, and why is it necessary at the Data Link layer?**
Framing is the process of dividing a continuous stream of bits (from the Physical layer) into discrete, manageable units called frames, by adding header and trailer information that marks the frame's boundaries. It's necessary because the Physical layer has no concept of "where one unit of data ends and another begins" — it just deals with raw bits, so the Data Link layer must impose this structure to allow meaningful data exchange.

**Q2. What is Bit Stuffing, and why is it used?**
Bit Stuffing is a technique used to prevent the actual data payload from accidentally containing the same bit pattern used as the frame's boundary flag (commonly `01111110`). Whenever five consecutive 1s appear in the data, the sender automatically inserts (stuffs) an extra 0 after them; the receiver removes this stuffed 0, ensuring the flag pattern only ever appears at genuine frame boundaries, never within the actual data.

**Q3. What is the key limitation of a simple Parity Bit for error detection?**
A single parity bit can only reliably detect an ODD number of bit errors (1, 3, 5, etc.) — if an EVEN number of bits are flipped during transmission (e.g., 2 bits), the overall parity (even or odd count of 1s) remains unchanged, so the error goes completely undetected.

**Q4. How does CRC (Cyclic Redundancy Check) work at a high level?**
CRC treats the data as a binary polynomial and divides it (using modulo-2/XOR arithmetic) by a fixed, predetermined generator polynomial. The remainder of this division becomes the CRC code, which is appended to the data before transmission. The receiver performs the identical division on the received data (including the CRC); if the remainder comes out to zero, the data is assumed to be error-free — any non-zero remainder indicates a transmission error occurred.

**Q5. Why is CRC considered stronger/more reliable than a simple Checksum or Parity Bit?**
CRC's mathematical properties (based on polynomial division) make it extremely effective at detecting burst errors (multiple consecutive corrupted bits), which are very common in real-world transmission due to noise affecting a duration of the signal. Parity bits can only reliably catch odd-numbered single/multi-bit errors, and simple checksums, while better than parity, are still less robust against certain error patterns than CRC's mathematically rigorous polynomial-based approach.

**Q6. What is the key difference between Error Detection and Error Correction?**
Error Detection techniques (like Parity, Checksum, CRC) can only determine THAT an error has occurred, requiring the receiver to request retransmission of the corrupted data. Error Correction techniques (like Hamming Code) go further — they can determine WHERE (which specific bit) the error occurred, and automatically fix it without needing to request retransmission at all.

**Q7. How does Hamming Code determine the number of redundant (parity) bits needed?**
Using the formula `2^r ≥ m + r + 1`, where `m` is the number of actual data bits and `r` is the number of redundant bits needed — you find the smallest value of `r` that satisfies this inequality, ensuring there are enough unique parity-check combinations to identify the position of any single-bit error among all the data and parity bits combined.

**Q8. Why are Hamming Code's redundant bits placed specifically at positions that are powers of 2 (1, 2, 4, 8, ...)?**
Because each redundant bit at a power-of-2 position is responsible for checking a specific, non-overlapping SET of bit positions (based on their binary representation) — this specific placement ensures that when you combine the results of all the parity checks (which ones "failed"), their positions directly encode (in binary) the exact position of the single erroneous bit, allowing precise error location and correction.

**Q9. In what scenarios would you prefer Error Correction (like Hamming Code) over simple Error Detection with retransmission?**
When retransmission is expensive, slow, or impractical — such as in satellite communication (where round-trip retransmission delay is very high), deep-space communication, or in computer memory systems (ECC RAM), where "retransmitting" isn't even a meaningful concept and errors must be corrected on the fly to maintain system reliability.

**Q10. What is a "burst error," and why is it more realistic to consider in real-world networking scenarios than isolated single-bit errors?**
A burst error is when two or more CONSECUTIVE bits are corrupted during transmission, rather than a single isolated bit. It's more realistic because real-world noise sources (electromagnetic interference, signal attenuation, physical disturbances) typically affect a signal over some duration of time, not just a single instantaneous bit — meaning that when errors occur in practice, they often affect a contiguous run of bits rather than just one bit in isolation, which is exactly the type of error CRC is specifically designed to catch effectively.

**Q11. If a receiver computes a CRC remainder of zero, does that guarantee the data was transmitted without any errors?**
Not with absolute 100% certainty, but with extremely high probability for a well-chosen generator polynomial. CRC is a probabilistic error-detection method — it's mathematically possible (though highly unlikely with a good generator polynomial) for a specific pattern of multiple bit errors to coincidentally still produce a zero remainder, meaning the error would go undetected. However, CRC's generator polynomials are specifically designed to make this probability extremely small for common error patterns (especially burst errors), making it highly reliable in practice.

**Q12. Walk through, at a high level, how the receiver would use Hamming Code to both detect AND correct a single-bit error.**
The receiver recomputes each parity check (r1, r2, r4, etc.) using the same logic the sender used, comparing against the received data. If all checks pass (result in 0), no error occurred. If one or more checks fail, the receiver combines the POSITIONS of the failed checks (treating them as a binary number) — this binary sum directly gives the exact bit position where the single error occurred, which the receiver can then simply flip/invert to correct the error, all without needing to request retransmission from the sender.
