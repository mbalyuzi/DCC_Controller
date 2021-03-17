# DCC_Controller
An Arduino DCC Controller, using timer driven interrupts to generate the bitstream.

This is a work in progress.

## Principles

The DCC bitstream comprises a cycle of a full cycle of high and low to represent a bit, where the cycle time is either 2 x 58uS to represent a "1" or 2 x 100uS to represent a "0". (That is e.g. 58uS high, 58uS low for a complete bit).

A DCC packet comprises a preamble consisting of a number of "1"s, a number of bytes, each preceeded by a "0", a checksum byte (with preceeding "0"), and a final "1" bit (which can be part of the subsequent packet's preamble.

### AVR timer

The AVR microprocessor has a timer module that can be configured in a number of different ways. One of these, CTC (Clear Timer on Compare) has a free-running timer that resets to 0 when matching a value set in a comparison register. Additionally, on clear, it can toggle an output pin and generate an interrupt.

Setting an appropriate clock rate allows a 58uS or 100uS period between inversions of the output pin. Setting the comparison register to match at 58uS or 100uS, and updating it every other interrupt appropriately to represent the next bit will therefore generate a DCC bitstream.

### Bitstream generation

On every other interrupt the interrupt handler needs to fetch the next bit. It does this by calling a function that maintains a state machine to represent progress through the packet. Each byte is returned in Big-endian form, one bit at a time.

