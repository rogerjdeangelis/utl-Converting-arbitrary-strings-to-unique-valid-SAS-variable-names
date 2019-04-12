# utl-Converting-arbitrary-strings-to-unique-valid-SAS-variable-names
Converting arbitrary strings to unique valid SAS variable names
    Converting arbitrary strings to unique valid SAS variable names.             
                                                                                 
    I am not interested in nliteral, anyalpha or other methods that do           
    no ensure uniqueness.                                                        
                                                                                 
    I need to create valid SAS names from arbitrary character strings.           
                                                                                 
    I thought there was a SCL function to convert a                              
    strings to a valid SAS name?    
    
    Recent Comments and solution by Paul Dorfman                                             
    Paul Dorfman                                                                            
    sashole@bellsouth.net                                                                   
                                                                                            
    Roger,                                                                                  
                                                                                            
    Methinks it's as simple as:                                                             
    uniqueName = translate (put (md5 (AnyString), $hex32.), "HGKLMNXYZ", "0123456789") ;    
    Of course, we only need to replace the *leftmost* hex digit in the response of the      
    PUT function in case it is 0-9; elsewhere, the digits are fine. But what the heck,      
    we can just replace all the digits in the result of PUT with letters it doesn't have    
    in one fell swoop of the TRANSLATE action. Since the result of PUT contains only        
    letters A-F, the uniqueness is preserved.                                               
    ruth be told, this is a one-way approach: You can't take uniqueName generated this      
    way and convert it back to AnyString; so, you will have to maintain a cross-reference   
    table. However, this is as easy as storing the pairs in a character format.             
    Best regards                                                                            
      
          
          
          
    *_                   _                                                       
    (_)_ __  _ __  _   _| |_                                                     
    | | '_ \| '_ \| | | | __|                                                    
    | | | | | |_) | |_| | |_                                                     
    |_|_| |_| .__/ \__,_|\__|                                                    
            |_|                                                                  
    ;                                                                            
                                                                                 
    data have;                                                                   
      informat strng $32.;                                                       
      input strng;                                                               
    cards4;                                                                      
    MY{}MESSY%(0_NAME                                                            
    MARY$@12.2(Mon)                                                              
    DOB12/23/2018T11:30AM                                                        
    ;;;;                                                                         
    run;quit;                                                                    
                                                                                 
    /*                                                                           
    Up to 40 obs WORK.HAVE total obs=3                                           
                                                                                 
      KEY            STRNG                                                       
                                                                                 
       1     MY{}MESSY%(0_NAME                                                   
       2     MARY$@12.2(Mon)                                                     
       3     DOB12/23/2018T11:30AM                                               
    */                                                                           
                                                                                 
    *            _               _                                               
      ___  _   _| |_ _ __  _   _| |_                                             
     / _ \| | | | __| '_ \| | | | __|                                            
    | (_) | |_| | |_| |_) | |_| | |_                                             
     \___/ \__,_|\__| .__/ \__,_|\__|                                            
                    |_|                                                          
    ;                                                                            
                                                                                 
    Up to 40 obs WORK.WANT total obs=3                                           
                                                                                 
    Obs            STRNG            VALIDNAME                                    
                                                                                 
     1     MY{}MESSY%(0_NAME        MY_MESSY_0_NAME                              
     2     MARY$@12.2(Mon)          MARY_12_2_MON_                               
     3     DOB12/23/2018T11:30AM    DOB12_23_2018T11_30AM                        
                                                                                 
    *                                                                            
     _ __  _ __ ___   ___ ___  ___ ___                                           
    | '_ \| '__/ _ \ / __/ _ \/ __/ __|                                          
    | |_) | | | (_) | (_|  __/\__ \__ \                                          
    | .__/|_|  \___/ \___\___||___/___/                                          
    |_|                                                                          
    ;                                                                            
                                                                                 
    proc transpose data=have out=xpo(drop=_:);                                   
      var strng;                                                                 
      id strng;                                                                  
    run;quit;                                                                    
                                                                                 
    data want;                                                                   
      set xpo;                                                                   
      array nam _character_;                                                     
      do over nam;                                                               
         strng=nam;                                                              
         validname=prxchange('s/(_)\1+/_/',99,vname(nam));                       
         output;                                                                 
      end;                                                                       
      keep strng validname;                                                      
    run;                                                                         
                                                                                 
                                                                                 
