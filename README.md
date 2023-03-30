# epcx - Chris Brown, 03/30/2023

epcx is a script for the Tarski system.  Tarski itself (as of version 1.32)
does not provide a good "black-box" quantifier elimination command.  It
provides tools, like qepcad-api-cal, qfr, clear-assignments, normalize, etc,
but there is no overarching command that takes a quantified formula as
input and makes intelligent decisions about how to combine these commands to
efficiently produce a quantifier-free equivalent.  This file defines the
*epc* command, which does that - still in a very simple minded way - for
existentially quantified input formulas.  As a very simple example, consider
the formula:

`[ex x,y[a x^2 y^3 + x y - x + y^2 < 1 /\ y = 2 \/ x^2 + y^2 < a]]`

If Tarski sends this straight to qepcad, CAD-based QE will be applied to
this entire formula.  However, this can be rewritten as 

`[ex x,y[a x^2 y^3 + x y - x + y^2 < 1 /\ y = 2]] \/ [[ex x,y[x^2 + y^2 < a]]`

... which can be solved as two smaller QE problems, which is more efficient.
Moreover, in the first subformula, we can eliminate y by simply substituting
y = 2, which gives

`[ex x[8 a x^2 + 2 x - x < -3 ]] \/ [[ex x,y[x^2 + y^2 < a]]`

... which now is solved with two very simple calls to qepcad-api-call.  The
"epc" attempts to do these kinds of splittings and simplifications before
calling on QepcadB.
