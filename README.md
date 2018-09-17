# acc
  
Life is fun, ATS is fun so, ATS error reporting should be fun too. 


Pretty-printer/wrapper for the ats compiler/typechecker


## Motivation

Primarily seeks to make error reporting easier to read/understand and increase programming productivity. Places an importance of being memory frugal and explicit memory management (no memory leaks). The application wraps patsopt, patscc and myatscc into one executable. The overall goal is to create a unified tool surrounding the ats compiler and other utilities.


## Build

	make

## Install

	make install

NOTE: will install to $PATSHOME/bin (which should already be on PATH)

## Testing

	make testrun

Also, see TEST directory.

- TEST/TESTS/*.dats files (each contains errors)


## Example

      acc -my foo.dats


## Usage

	acc [whichcc] <filename.[d|s]ats>

whichcc:

    | -my         => myatscc        // generic myatscc
    | -pc         => patscc         // generic patscc
    | -po         => patsopt        // generic patsopt

    | -pm         => PATS           // suppot for malloc
    | -gc         => PATS_GC        // for garbage collection
    | -lm         => PATS_LM        // to link math library

    | -tcats      => patscc -tcats  // typecheck
    | -tc         => patscc -tcats  // alias
    | -fly        => patscc -tcats  // for use in emacs
    | -c          => patscc -tcats  // (experimental) colorized 
  
    | --myatscc   => myatscc 
    | --patscc    => patscc 
    | --patsopt   => patsopt 
  
    | --malloc    => see -pm
    | --garbage   => see -gc
    | --math      => see -lm
    | --typecheck => see -tcats or -tc
    | --flycheck  => see -fly
    | --color     => see -c (experimental)

    where {
      PATS    = "patscc -D_GNU_SOURCE -DATS_MEMALLOC_LIBC"
      PATS_GC = "patscc -D_GNU_SOURCE -DATS_MEMALLOC_GCBDW"
      PATS_LM = "patscc -D_GNU_SOURCE -DATS_MEMALLOC_LIBC -lm"
    }

filename:

    valid file extensions: .dats or .sats

  example:

    acc -my foo.dats

    acc -gc foo.dats -o foo

    note: 
      one can also construct existing options. 
      for example, to use patscc with familiar options
      acc -pc -DATS_GNU_SOURCE -DATS_MEMALLOC_LIBC -O2 foo.dats -o foo

      or for patsopt,
      acc -po --output foo.tags --taggen -d foo.dats

  error hints: 

     y <~ x  :  'x' should be 'y' 
     y <?~ x :  'x' should (most likely) be 'y'

          #  :  prefix for external value or external kind 
                (S2Eextype | S2Eextkind) 

        [n]  :  existentially qualified type n (S2Eexi)
        inf  :  intinf (S2Eintinf)
       uni.  :  universally quantified type (S2Euni)

      g0int  :  g0int_t0ype   (unindexed integer)
      g1int  :  g1int_int_t0ype (indexed integer)

      XXXX-
      XXXXX  :  where X is an integer (I believe is a tag of some sort)



## Tips

  Currently handles only a subset of error expressions from "ATS2/src/pats_staexp2.sats"
  - expect additions
  - output format will improve
  - need advice for general format

#####  some notation :: 
    
    *         #  :  prefix for external value or external kind (S2Eextype | S2Eextkind) 
    *       [n]  :  existentially qualified type n (S2Eexi)
    *       inf  :  intinf (S2Eintinf)
    *      uni.  :  universally quantified type (S2Euni)
    *    y <~ x  :  x should be y 
    *   y <?~ x  :  x should (most likely) be y    
    *   "g0int"  :  "g0int_t0ype"
    * "g0float"  :  "g0float_t0ype"
    *  "g0uint"  :  "g0uint_t0ype"
    *   "g1int"  :  "g1int_int_t0ype"
    *    "char"  :  "char_t0ype"
    *    "char"  :  "char_int_t0ype"
    *  "string"  :  "string_type"
    *  "string"  :  "string_int_type" 
    * "strnptr"  :  "strnptr_addr_int_vtype"
    *    "list"  :  "list_t0ype_int_type"
    * "list_vt"  :  "list_vt0ype_int_vtype"
    *    XXXXX   :  where X is an integer (I believe is a tag of some sort)
     


## Future 

  * The ideal form of the executable is one executable including a suite of
    smaller utilities (similar to haskells 'cabal').

	######  Future Additions :: 
    	* 'describe' : a utility to describe a particular error message 
    
	    * option to print only first error
  
    	* REPL-like evaluator (currently developing and refining)

    	* Code formatter
    
    	* Infer file type instead of having to explicitly pass the file type flag 
      		( -d (for .dats) | -s (for .sats) )

	    * Built-in options that automagically jsonize, depgen, taggen as opposed to
    	    "patsopt --jsonize -d foo.dats" | "patsopt --jsonize -s foo.sats"

        	  acc --jsonize ( FILE.[d|s]ats ) 
              acc --depgen  ( FILE.[d|s]ats ) 
              acc --taggen  ( FILE.[d|s]ats )
        
	    * Incorporate other compilation tools that target languages other than C.
    	  options for -js / --atscc2js (for javascript) ...


***

## Example

patscc -tcats ./TEST/cfail.dats

/.../ats-acc/TEST/cfail.dats: 570(line=35, offs=5) -- 610(line=35, offs=45): error(3): unsolved constraint: C3NSTRprop(C3TKmain(); S2Eeqeq(S2Eapp(S2Ecst(sub_int_int); S2Evar(r$14475$14476(23038)), S2Evar(r$14479$14480(23042))); S2Eapp(S2Ecst(add_int_int); S2Evar(r$14475$14476(23038)), S2Evar(r$14479$14480(23042)))))
typechecking has failed: there are some unsolved constraints: please inspect the above reported error message(s) for information.
exit(ATS): uncaught exception: _2home_2hwxi_2Research_2ATS_2dPostiats_2src_2pats_error_2esats__FatalErrorExn(1025)


--------------------------------------------

acc -tcats ./TEST/cfail.dats

/.../ats-acc/TEST/cfail.dats: 570(line=35, offs=5) -- 610(line=35, offs=45)

   error(3):
   unsolved constraint

      add_int_int(r(23038), r(23042))  <?-  sub_int_int(r(23038), r(23042))

exit(ATS): uncaught exception: (1025)


*****

###### NOTE:  This is LARGELY an unfinished product and is still being actively developed.


... give it a whirl...
