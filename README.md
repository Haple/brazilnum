Validate Brazilian Identification Numbers
=========================================

Python functions for working with CNPJ, CEI, CPF, and PIS/PASEP numbers, which
identify firms and people in Brazil.

Installation
------------

    pip install brazilnum

Usage Examples
--------------

Validate a CNPJ number for a firm, in this case Telefônica Brasil:

    >>> from brazilnum.cnpj import validate_cnpj
    >>> validate_cnpj('02.558.157/0001-62')
    True

Validate a CEI number, used for businesses that do not require a CNPJ:

    >>> from brazilnum.cei import validate_cei
    >>> validate_cei('11.583.00249/85')
    True

Validate CPF and PIS/PASEP numbers for individuals:

    >>> from brazilnum.pis import validate_pis
    >>> from brazilnum.cpf import validate_cpf
    >>> validate_pis('125.6124.131-0')
    True
    >>> validate_cpf('968.811.342-58')
    True

The functions work even if the numbers are not formatted:

    >>> validate_cnpj('02558157000162')
    True
    >>> validate_cei('115830024985')
    True
    >>> validate_pis('12561241310')
    True
    >>> validate_cpf('96881134258')
    True

The validation functions return ``False`` for invalid identifiers:

    >>> validate_cnpj('02.558.157/0001-55')
    False
    >>> validate_cei('11.583.00249/84')
    False
    >>> validate_pis('111.6124.131-0')
    False
    >>> validate_cpf('327.861.067-97')
    False

Use the format function when displaying identifiers:

    >>> from brazilnum.cnpj import format_cnpj
    >>> format_cnpj('02558157000162')
    '02.558.157/0001-62'

    >>> from brazilnum.cei import format_cei
    >>> format_cei('115830024985')
    '11.583.00249/85'

    >>> from brazilnum.pis import format_pis
    >>> format_pis('12561241310')
    '125.6124.131-0'

    >>> from brazilnum.cpf import format_cpf
    >>> format_cpf('96881134258')
    '968.811.342-58'

There is also a helper function to remove formatting from identifiers:

    >>> from brazilnum.cnpj import clean_cnpj
    >>> clean_cnpj('02.558.157/0001-62')
    '02558157000162'
   
    >>> from brazilnum.cei import clean_cei
    >>> clean_cei('11.583.00249/85')
    '115830024985'

    >>> from brazilnum.pis import clean_pis
    >>> clean_pis('125.6124.131-0')
    '12561241310'

    >>> from brazilnum.cpf import clean_cpf
    >>> clean_cpf('968.811.342-58')
    '96881134258'

Your data source might store CNPJ and CEI as integers, in which case leading
zeros will be missing. You can pad and validate these numbers in one step:

    >>> from brazilnum.cnpj import pad_cnpj
    >>> pad_cnpj(2558157000162, validate=True)
    '02558157000162'
    
    >>> pad_cnpj(2558157000155, validate=True)
    Traceback (most recent call last):
        ...
    ValueError: Invalid CNPJ: 02558157000155

    >>> from brazilnum.cei import pad_cei
    >>> pad_cei(115830024985, validate=True)
    '115830024985'

You can also skip the validation step:

    >>> pad_cnpj(2558157000155, validate=False)
    '02558157000155'

Padding works the same way for PIS/PASEP and CPF numbers:

    >>> from brazilnum.pis import pad_pis
    >>> pad_pis(12561241310, validate=True)
    '12561241310'

    >>> pad_pis(11161241310, validate=True)
    Traceback (most recent call last):
        ...
    ValueError: Invalid PIS/PASEP: 11161241310


    >>> from brazilnum.cpf import pad_cpf
    >>> pad_cpf(4193675866, validate=True)
    '04193675866'

    >>> pad_cpf(4193675867, validate=True)
    Traceback (most recent call last):
        ...
    ValueError: Invalid CPF: 04193675867

If you're interested in just the check digits (i.e. last digits), use the
``cnpj_check_digits``, ``cei_check_digit``, ``cpf_check_digits``, and
``pis_check_digit`` functions to find them:

    >>> from brazilnum.cnpj import cnpj_check_digits
    >>> cnpj_check_digits('02.558.157/0001-62')
    (6, 2)

    >>> from brazilnum.cei import cei_check_digit
    >>> cei_check_digit('11.583.00249/85')
    5

    >>> from brazilnum.cpf import cpf_check_digits
    >>> cpf_check_digits('041.936.758-66')
    (6, 6)

    >>> from brazilnum.pis import pis_check_digit
    >>> pis_check_digit('125.6124.131-0')
    0

CNPJ check digits are calculated from the first 12 digits:

    >>> cnpj_check_digits('025581570001')
    (6, 2)

The CEI check digit is calculated from the first 11 digits:

    >>> cei_check_digit('11583002498')
    5

CPF check digits are calculated from the first 9 digits:

    >>> cpf_check_digits('041936758')
    (6, 6)

The PIS/PASEP check digit is calculated from the first 10 digits:

    >>> pis_check_digit('1256124131')
    0

The first 8 digits of the CNPJ identify a firm, the next 4 digits identify a
specific business establishment owned by that firm.  The headquarters is often
establishment 0001.  The ``cnpj_from_firm_id`` function will create a full CNPJ
from the first 8 digits and a given establishment number:

    >>> from brazilnum.cnpj import cnpj_from_firm_id
    >>> cnpj_from_firm_id('02.558.157')
    '02558157000162'

    >>> cnpj_from_firm_id('02.558.157', establishment='0002')
    '02558157000243'

CNPJ can be parsed into firm, establishment, and check digit components:

    >>> from brazilnum.cnpj import parse_cnpj
    >>> parse_cnpj('02.558.157/0001-62')
    CNPJ(cnpj='02.558.157/0001-62', firm='02.558.157', establishment='0001', check='62', valid=True)

    >>> parse_cnpj('02.558.157/0001-62', formatted=False)
    CNPJ(cnpj=2558157000162, firm=2558157, establishment=1, check=(6, 2), valid=True)

If you need random CNPJ for database testing, use the ``random_cnpj`` function,
which can return either unformatted or formatted identifiers:

    from brazilnum.cnpj import random_cnpj
    random_cnpj()       # for a random, formatted CNPJ
    random_cnpj(False)  # for a random, unformatted CNPJ

Use ``random_cei`` for random CEI identifiers:

    from brazilnum.cei import random_cei
    random_cei()

The same thing is possible for PIS/PASEP using the ``random_pis`` function:

    from brazilnum.pis import random_pis
    random_pis()

The ``random_cpf`` function will generate a random CPF identifier:

    from brazilnum.cpf import random_cpf
    random_cpf()

