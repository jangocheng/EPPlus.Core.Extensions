# **EPPlus.Core.Extensions** [![Build status](https://ci.appveyor.com/api/projects/status/cdhoa8m20k2k71ke?svg=true)](https://ci.appveyor.com/project/eraydin/epplus-core-extensions) [![codecov](https://codecov.io/gh/eraydin/EPPlus.Core.Extensions/graph/badge.svg)](https://codecov.io/gh/eraydin/EPPlus.Core.Extensions)

### **Installation** [![NuGet version](https://badge.fury.io/nu/EPPlus.Core.Extensions.svg)](https://badge.fury.io/nu/EPPlus.Core.Extensions)

It's as easy as `PM> Install-Package EPPlus.Core.Extensions` from [nuget](http://nuget.org/packages/EPPlus.Core.Extensions)

### **Dependencies**

**.NET Framework 4.6.1**
      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*EPPlus >= 4.1.1*

**.NET Standard 2.0**
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*EPPlus.Core >= 1.5.4*

### **Documentation and Examples**

The project will be documented soon. For now, just look at the test project (EPPlus.Core.Extensions.Tests). It has just enough examples to show you how to use these extension methods. 

##### **Basic usage**

```cs
public class PersonDto
    {      
        [ExcelTableColumn("First name")]
        [Required(ErrorMessage = "First name cannot be empty.")]
        [MaxLength(50, ErrorMessage = "First name cannot be more than {1} characters.")] 
        public string FirstName { get; set; }

        [ExcelTableColumn("Last name")]       
        public string LastName { get; set; }
        
        [ExcelTableColumn(3)]
        [Range(1900, 2050, ErrorMessage = "Please enter a value bigger than {1}")]
        public int YearBorn { get; set; }
        
        public decimal NotMapped { get; set; }
    }
    
// Converting from Excel to list of objects

    // Direct usage: 
        excelPackage.ToList<PersonDto>(configuration => configuration.SkipCastingErrors());

    // Specific worksheet: 
        excelPackage.GetWorksheet("Persons").ToList<PersonDto>();  
    
// From list to Excel package

    List<PersonDto> persons = new List<PersonDto>();

    // Direct usage
        ExcelPackage excelPackage = persons.ToExcelPackage();
        byte[] excelPackageXlsx = persons.ToXlsx();
       
    // With configuration

    List<PersonDto> pre50 = persons.Where(x => x.YearBorn < 1950).ToList();
    List<PersonDto> post50 = persons.Where(x => x.YearBorn >= 1950).ToList();
        
    ExcelPackage excelPackage = pre50.ToWorksheet("< 1950")
                             .WithConfiguration(configuration => configuration.WithColumnConfiguration(x => x.AutoFit()))
                             .WithColumn(x => x.FirstName, "First Name")
                             .WithColumn(x => x.LastName, "Last Name")
                             .WithColumn(x => x.YearBorn, "Year of Birth")
                             .WithTitle("< 1950")
                             .NextWorksheet(post50, "> 1950")
                             .WithColumn(x => x.LastName, "Last Name")
                             .WithColumn(x => x.YearBorn, "Year of Birth")
                             .WithTitle("> 1950")
                             .ToExcelPackage(); 
```