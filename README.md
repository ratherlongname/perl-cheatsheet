# Learning Perl

[Learn Perl in about 2 hours 30 minutes](https://qntm.org/perl_en)
[Perldoc - Perl documentation site](https://perldoc.perl.org/)

## Cheatsheet

Hello, World!
```perl
use strict;
use warnings;

print "Hello, World!\n";
```

Run perl script
```bash
perl hello_world.pl
```

Comments
```perl
# There are only single line comments in Perl
```

Scalar variable
```perl
my $ex_undef = undef;			# explicit undef
my $im_undef;				# implicit undef
my $number = 33.56;			# no difference between floats, ints
my $string = "Hello";
my $reference = \$number		# arrays, hashes can be referenced too
```

Booleans
```perl
# there are no booleans
# these scalars evaluate to false
my $false = undef;
my $false = 0;
my $false = "0";
my $false = "";
# functions return 1 for true, "" for false
```

Weak typing
```perl
# scalars behave as strings or numbers based on the operator used
# Numerical operators:  <,  >, <=, >=, ==, !=, <=>, +, *
# String operators:    lt, gt, le, ge, eq, ne, cmp, ., x
my $str1 = "4G";
my $str2 = "4H";

print $str1 .  $str2; # "4G4H"
print $str1 +  $str2; # "8" with two warnings
print $str1 eq $str2; # "" (empty string, i.e. false)
print $str1 == $str2; # "1" with two warnings

# The classic error
print "yes" == "no"; # "1" with two warnings; both values evaluate to 0 when used as numbers
```

Array variables
```perl
# 0 indexed lists of scalars
my @array = (
	"print",
	"these",
	"strings",
	"out",	# trailing comma is okay
);

print $array[0];	# use '$' sigil because scalar is being retrieved
print $array[5];	# returns undef, prints "" and raises a warning
print $array[-2];	# "strings"
print "This array has ".(scalar @array)."elements"; # "This array has 6 elements"
print "The last populated index is ".$#array;       # "The last populated index is 5"

# @ARGV stores the arguments with which the Perl script was invoked
```

Strings
```perl
my $string = "Hello, " . "World!";	# . for string concatenation

# variables can be interpolated into strings
print "$string Bye, World!";	# "Hello, World! Bye, World!"
print "ARR:\t @array";		# "ARR:     printthesestringsout"
# preventing interpolation
print "ARR:\t \@array";		# "ARR:     @array" ie. escape the sigil
print '@array\t $string';	# "@array\t $string" ie. use single quotes
```

Hash variables
```perl
# list of scalars indexed by strings
# keys have no underlying order
my %scientists = (
	"Newton"   => "Isaac",
	"Einstein" => "Albert",
	"Darwin"   => "Charles",
);

print $scientists{"Newton"};	# use '$' sigil because scalar is being retrieved
print $scientists{"Tesla"};	# returns undef, prints "" and raises a warning

# fat comma '=>' is syntactic sugar for comma ','
# every even element of list is taken to be string automatically

my @scientists = %scientists;	# yields array twice as long as hash
				# pairs will be preserved but overall order will not
```

Lists
```perl
# ephemeral value that can be assigned to an array or hash variable

# nested list cannot exist, it is flattened automatically
my %hash = (
	"beer" => "good",
	"bananas" => (
		"green"  => "wait",
		"yellow" => "eat",
	),
);		# raises warning because hash was declared with 7 (odd) element list

print $hash{"beer"};    # "good"
print $hash{"bananas"}; # "green"
print $hash{"wait"};    # "yellow"
print $hash{"eat"};     # returns undef, prints "" and raises a warning

my @many_scientists = (@scientists, %scientists, ("Nikola", "Tesla"));	# concatenation
```

Context
```perl
# every expression in Perl is evaluated either in scalar context or list context
# depending on whether it is expected to produce a scalar or a list

my $scalar = "Mendeleev";				# scalar context
my @array = ("Alpha", "Beta", "Gamma", "Pie");		# list context
my %hash = ("Alpha" => "Beta", "Gamma" => "Pie");	# list context

# scalar expression in list context is silently converted to single-element list
my @array = "Mendeleev";	# same as ("Mendeleev")

# array expression in scalar context returns length of array
my @array = ("Alpha", "Beta", "Gamma", "Pie");
my $scalar = @array;
print $scalar;	# "4"

# list expression in scalar context returns last element of list
my $scalar = ("Alpha", "Beta", "Gamma", "Pie");
print $scalar;	# "Pie"

# print function accepts unlimited list of arguments
# and evaluates all of them in list context
my @array = ("Alpha", "Beta", "Goo");
print @array, 98;	# "AlphaBetaGoo98";

# CAUTION
# many built-in functions display radically different behaviour
# depending on the context in which they are evaluated

print scalar @array;	# "3" ie. force scalar context with scalar built in function
```

References
```perl
# arrays, hashes cannot contain arrays, hashes as elements. only scalars
# hence references are used

my @array = ("Tesla", "Newton", "Curie");
my $ref = \@array;
print @array;	# "TeslaNewtonCurie"
print $ref;	# "ARRAY(0x55a20cea4568)"
print @{ $ref };	# "TeslaNewtonCurie" ie. variable_name is same as {reference_name}
print @$ref;	# "TeslaNewtonCurie" ie. braces can be skipped if unambiguous
print ${ $ref }[1];	# "Newton"
print $ref->[1];	# "Newton" ie. arrow operator '->' to access without dereferencing
```

Nested data structures
```perl
# [] to make anonymous array and return its reference
# {} to make anonymous hash and return its reference

my %account = (
	"number" => "31415926",
	"opened" => "3000-01-01",
	"owners" => [
		{
			"name" => "Philip Fry",
			"DOB"  => "1974-08-06",
		},
		{
			"name" => "Hubert Farnsworth",
			"DOB"  => "2841-04-09",
		},
	],
);

print "Account #", $account{"number"};
print "Opened on ", $account{"opened"};
print "Joint owners:";
print $account{"owners"}->[0]->{"name"}, $account{"owners"}->[0]->{"DOB"};
print $account{"owners"}->[1]->{"name"}, $account{"owners"}->[1]->{"DOB"};

# CAUTION
my @array1 = (1, 2, 3, 4, 5);	# correct way
print @array1; # "12345"
my @array2 = [1, 2, 3, 4, 5];	# incorrect way
print @array2; # "ARRAY(0x182c180)" ie. array contains one scalar reference to 5 element array
```

Conditionals
```perl
# if conditions are evaluated in scalar context

# if ... elsif ... else ...
my $word = "antidisestablishmentarianism";
my $strlen = length $word;

if($strlen >= 15) {
	print $word, " is a very long word";
} elsif(10 <= $strlen && $strlen < 15) {
	print $word, " is a medium-length word";
} else {
	print $word, " is a short word";
}

# statement if condition
print $word, " is actually enormous" if $strlen >= 20;

# unless ... else ...
my $temperature = 20;

unless($temperature > 30) {
	print $temperature, " degrees Celsius is not very hot";
} else {
	print $temperature, " degrees Celsius is actually pretty hot";
}

# statement unless condition
print "Oh no it's too cold" unless $temperature > 15;

# ternary operator
my $gain = 48;
print "You gained ", $gain, ($gain == 1 ? " point!" : " points!");
```

Loops
```perl
# while ...
my $i = 0;
while($i < scalar @array) {
	print $i, ": ", $array[$i];
	$i++;
}

# until ...
my $i = 0;
until($i >= scalar @array) {
	print $i, ": ", $array[$i];
	$i++;
}

# do ... while ...
my $i = 0;
do {
	print $i, ": ", $array[$i];
	$i++;
} while ($i < scalar @array);

# do ... until ...
my $i = 0;
do {
	print $i, ": ", $array[$i];
	$i++;
} until ($i >= scalar @array);

# C style for loops
for(my $i = 0; $i < scalar @array; $i++) {
	print $i, ": ", $array[$i];
}

# foreach (and for keywords are synonyms)
foreach my $word ( @sentence ) {
	print $word;
}

# range operator '..' for indices
foreach my $i ( 0 .. $#array ) {
	print $i, ": ", $array[$i];
}

# hashes cannot be iterated over
# hash keys can
foreach my $key (keys %scientists) {
	print $key, ": ", $scientists{$key};
}

# sort keys alphabetically to ensure order
foreach my $key (sort keys %scientists) {
	print $key, ": ", $scientists{$key};
}

# $_ is default iterator, used if no iterator is provided
# $_ is a built-in variable
foreach ( @array ) {
	print $_;
}

# statement foreach @array
# must use single statement and default iterator
print $_, "\n" foreach @array;

# Loop control
for my $instruction ( @program ) {
	last if $instruction eq "error";	# last is break
	next if $instruction eq "pass";	# next is continue
	doinstr $instruction;
}

# Optionally label can be provided to any loop
# and used with last, next
CANDIDATE: for my $candidate ( 2 .. 100 ) {
	for my $divisor ( 2 .. sqrt $candidate ) {
		next CANDIDATE if $candidate % $divisor == 0;
	}
	print $candidate." is prime\n";
}
```

Array functions
```perl
my @stack = ("Fred", "Eileen", "Denise", "Charlie");
print @stack; # "FredEileenDeniseCharlie"

print pop @stack; # "Charlie" ie. extract and return last element of array
print @stack;     # "FredEileenDenise"

push @stack, "Bob", "Alice";	# append elements to end of array
print @stack;			# "FredEileenDeniseBobAlice"

print shift @stack;	# "Fred" ie. extract and return first element of array
print @stack;		# "EileenDeniseBobAlice"

unshift @stack, "Hank", "Grace"; 	# insert elements at the beginning of the array
print @stack;				# "HankGraceEileenDeniseBobAlice"

# splice ARRAY,OFFSET,LENGTH,LIST
# removes and returns LENGTH elements from OFFSET onwards in ARRAY and replaces with LIST
# pop, push, shift, unshift are all special cases of splice
print @stack;					# "HankGraceEileenDeniseBobAlice"
print splice(@stack, 1, 4, "<<<", ">>>");	# "GraceEileenDeniseBob"
print @stack;					# "Hank<<<>>>Alice"

# map {operation} ARRAY
# applies operation to each scalar $_ in ARRAY, returns list of new values
my @array = ("Tesla", "Newton", "Curie");
print map { uc $_ } @array;	# "TESLANEWTONCURIE"

# grep {condition} ARRAY
# applies condition to each scalar $_ in ARRAY, returns list of scalars that evaluated true
print grep { length $_ == 5 } @array;	# "TeslaCurie"

# sort { BLOCK } LIST
# BLOCK receives $a, $b as inputs and should return
# -1 if $a is less than $b, 0 if they are equal, 1 if $a is greater than $b
# cmp does this for strings, spaceship operator '<=>' does this for numbers
# cmp is default if BLOCK is not supplied
my @elevations = (19, 1, 2, 100, 3, 98, 100, 1056);
print join ", ", sort { $a cmp $b } @elevations; # "1, 100, 100, 1056, 19, 2, 3, 98"
print join ", ", sort { $a <=> $b } @elevations; # "1, 2, 3, 19, 98, 100, 100, 1056"
# custom subroutine can be passed if comparison logic is complex
sub comparator {
	# lots of code...
	# return -1, 0 or 1
}
print join ", ", sort comparator @elevations;
```

Built-in functions
```perl
# Perl has lots of built-in functions
# skim through them here https://perldoc.perl.org/perlfunc.html
```

User-defined subroutines
```perl
# by convention, always call with parenthesis eg. hyphenate();
# always accept the same input: a list of scalars
# accessed via built-in variable @_ inside the subroutine
# a hash with N elements is taken as a list with 2N elements
sub hyphenate {
  my $word = shift @_;
  $word = join "-", map { substr $word, $_, 1 } (0 .. (length $word) - 1);
  return $word;
}
print hyphenate("exterminate"); # "e-x-t-e-r-m-i-n-a-t-e"

# Perl calls by reference
my $x = 7;
sub reassign {
  $_[0] = 42;
}
reassign($x);
print $x; # "42"

reassign(8); # error occurs and execution halts

# Unpacking @_ before use
print left_pad("hello", 10, "+"); # "+++++hello"
# shift, recommended upto 4 arguments
sub left_pad {
	my $oldString = shift;	# shift works on @_ by default
	my $width     = shift;
	my $padChar   = shift;
	my $newString = ($padChar x ($width - length $oldString)) . $oldString;
	return $newString;
}
# multiple simultaneous scalar assignments, recommended upto 4 arguments
sub left_pad {
	my ($oldString, $width, $padChar) = @_;
	my $newString = ($padChar x ($width - length $oldString)) . $oldString;
	return $newString;
}
# hash, for subroutines with large or complex usage of arguments
print left_pad("oldString" => "pod", "width" => 10, "padChar" => "+");
sub left_pad {
	my %args = @_;
	my $newString = ($args{"padChar"} x ($args{"width"} - length $args{"oldString"}))
			 . $args{"oldString"};
	return $newString;
}

# wantarray
# true if subroutine is called in list context, false in scalar context, undefined in void context
sub contextualSubroutine {
	return ("Everest", "K2", "Etna") if wantarray;
	return 3;
}
```

System calls
```perl
# processes in most systems conclude with a 16 bit status word
# higher 8 bits constitute a return code, 0 for success and 1-255 for various degrees of failure
exit 0; # stop current script with return code 0

# Perl spawns a child process, waits for it to be done, then resumes
# built-in variable $? is populated with status word of child process

# backticks `` to run command
# returns stdout output as single string in scalar context and
# list of strings containing each line of output, in list context
my $text = `perl anotherscript.pl foo bar baz`;
print $text; # "foobarbaz"
## anotherscript.pl
# use strict;
# use warnings;
# 
# print @ARGV;
# exit 37;

# system built-in function
# returns status word
my $sw = system "perl", "anotherscript.pl", "foo", "bar", "baz";
$rc = $sw >> 8;
print $rc; # "37"
```

Files and file handles
```perl
# file handle is a reference to a specific location inside a specific file
# scalar variables hold file handles
my $f = "text.txt";
open(my $fh, "<", $f) || # '<' indicates read mode
die "Couldn't open '".$f."' for reading because: ".$!;	# $! stores error message from failed open

# readline
# returns one line with terminating '\n' (except maybe for last line of file)
while(!eof $fh) {	# eof built-in function
	my $line = readline $fh;
	chomp $line;	# removes terminating '\n' if any
	# process $line...
}
while(my $line = <$fh>) {	# <> is same as readline
	# process $line...
}
while(<$fh>) {
	# process $_... 
}
# CAUTION
while(my $line = readline $fh) {
	# process $line
}
# this will stop if $line is "0", so don't do this

# '>' is write mode, it clobbers by default
# '>>' is append to existing file
open(my $fh2, ">", $f)
|| die "Couldn't open '".$f."' for writing because: ".$!;
print $fh2 "The eagles have left the nest"; # note absence of comma ',' after $fh2
close $fh2;	# file handles also close automatically when they drop out of scope

# STDIN, STDOUT, STDERR are global constant file handles
my $name = <STDIN>;

# file tests
# -X built-in functions where X is upper or lowercase alphabet
-e "/usr/bin/perl";	# test whether file exists
-d "/usr/bin";		# test whether file is a directory
-f "/usr/bin/perl";	# test whether file is a plain file
```

Regular expressions
```perl


```

Modules and packages
```perl


```

BEGIN blocks
```perl
# BEGIN blocks are executed as soon as they are parsed
# even before the rest of the file has been parsed
use strict;
use warnings;
print "This is parsed first, printed second";
BEGIN {
	print "This is parsed second, printed first";
}
print "This is parsed after first print. It gets printed third";
# After executing BEGIN blocks, script may never execute if a parsing error is found
```

use
```perl
# use is a disguised BEGIN block
use Caterpillar;
# is same as
BEGIN {
	require Caterpillar;
	Caterpillar->import();
}

use Caterpillar ("crawl", "pupate");
# is same as
BEGIN {
	require Caterpillar;
	Caterpillar->import("crawl", "pupate");
}

use Caterpillar ();
# is same as
BEGIN {
	require Caterpillar;
}

# import is not a built-in function
# it must be implemented or inherited in Caterpillar package
```
