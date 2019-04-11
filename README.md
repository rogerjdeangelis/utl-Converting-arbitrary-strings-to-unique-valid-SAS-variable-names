# utl-Converting-arbitrary-strings-to-unique-valid-SAS-variable-names
Converting arbitrary strings to unique valid SAS variable names
    Converting arbitrary strings to unique valid SAS variable names.             
                                                                                 
    I am not interested in nliteral, anyalpha or other methods that do           
    no ensure uniqueness.                                                        
                                                                                 
    I need to create valid SAS names from arbitrary character strings.           
                                                                                 
    I thought there was a SCL function to convert a                              
    strings to a valid SAS name?                                                 
                                                                                 
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
                                                                                 
                                                                                 
