	#include <iostream>
	#include <fstream>
	#include <iomanip>
	using namespace std;

	void ReadInput(ifstream &, int [], float [50][5], int &, int &, int &);
	void PrintArray(ofstream &, int [], float [50][5], int &);
	void PrintNew(ofstream &, int [], float [50][5], char [], int &);
	void PrintOutput(ofstream &, int &);
	void SortArray(int [], float [50][5], int &, int &);
	void SortByAvg(int [], float [50][5], char [], int &, int &);
	void FindAvg(float [50][5], int &, int &);
	void Letter(float [50][5], char [], int &, int &);
	void Footer(ofstream &);
	void Header(ofstream &);

	int main()
	{
	ifstream input ("DATA4.TXT", ios::in);
	ofstream output ("OUTPUT", ios::out);
	output.setf(ios::fixed);
	output.precision(1); //limit the decimal places after a decimal to one
	int studentIDs[50]; //creates an array with fifty integer places for student ID's
	float testScores[50][5] = {0}; //creates a multi-dimensional array for test scores
	char letter[50];
	int EU; //keep track of how many elements of each array have been used
	int REU; //keep track of how many rows are used in each array
	int CEU; //keep track of how many columns are used in each array
	int check; //keep a variable to check whether there is an average and letter grade

	Header(output); //print the header
	ReadInput(input, studentIDs, testScores, REU, CEU, EU); //read input from file

	output << setw(10)<<"The original test data is:"<< endl;
	check = 0; //Don't print the average and letter grade columns
	PrintOutput(output, check); //Print the output header
	PrintArray(output, studentIDs, testScores,EU); //Print the original array
	output << endl;
	
	SortArray(studentIDs, testScores, REU, CEU); //Sort the array by ID from high to low
	output << setw(10)<<"The list of students sorted by ID Number high to low is:"<< endl;
	check = 0; //Don't print the average and letter grade
	PrintOutput(output, check); //Print the output header
	PrintArray(output, studentIDs, testScores, EU); //Print the sorted array
	output << endl;

	check = 1; //Print out the average and letter grade columns
	FindAvg(testScores, REU, CEU); //Find the average of each row of test scores
	Letter(testScores, letter, REU, CEU); //Create an array with a letter grade for each average
	output <<setw(10)<<"The list of students with their average and course grade is:"<< endl;
	output <<setw(68)<<"Test		Course"<< endl;
	PrintOutput(output, check); //print the output header, including average and letter grade
	PrintNew(output, studentIDs, testScores, letter, EU); //Print the list with avgs and grades

	SortByAvg(studentIDs, testScores, letter, REU, CEU); //Sort by test score from high to low
	output <<setw(10)<<"The list of students sorted by test average high to low is:"<< endl;
	output <<setw(68)<<"Test		Course"<< endl;
	PrintOutput(output,check); //Print the output header, including average and letter grade
	PrintNew(output, studentIDs, testScores, letter, EU); //Print the sorted list

	Footer(output); //Print the footer
	return 0;
	}


	void ReadInput(ifstream &infile,  int IDs[], float scores[50][5], int &REU, int &CEU, int &EU)
	{		//Recieves - the input file, array of student IDs, array of test scores, and EU
			//Task - reads in the student IDs and test scores from a file
			//Returns - filled arrays of student IDs and test scores
	int Num,i;
	float score;
	EU = 0;
	REU = 0;
	infile >> Num; //read ID number from file
	while(Num>0) // read in IDs and test scores until a zero is encountered
	{
		if(Num < 0) //check for sentinel 
			return;
		IDs[EU] = Num; //place ID number into first array
		infile >> score; //read test score from file
		EU++; 
		CEU = 0; //Initialize the columns used for use in the next row
		scores[REU][CEU] = score; //place test score into second array
		for(i=1; i<4; i++) //loop through each column in file
		{
		infile >> scores[REU][i]; //read in the test score
		}
		REU ++;
		infile >> Num; //read in the student ID number
	}
	CEU=i;
	return;
	}	


	void PrintArray(ofstream &outfile, int IDs[], float scores[50][5], int &elementsUsed)
			// Recieves - the output file, array of student IDs, array of test scores, and EU
			// Task - Prints the array of student IDs and test scores in columns
			// Returns - Nothing
	{
	int i,j;
	for(i=0; i<elementsUsed; ++i) //continue in this loop until there are no more values
	{
		// print the student id's and test scores on the same line with equal spacing
		outfile << setw(6) << IDs[i]<<" "; //print out the ID number
		for(j=0;j<4;j++) // Loop through all columns of array
		outfile << setw(11)<< scores[i][j]; //print out the test score
		outfile << endl;
	}
	return;
	}


	void PrintNew(ofstream &outfile, int IDs[], float scores[50][5], char letter[], int &elementsUsed)
			// Recieves - the output file, array of student IDs, array of test scores, and EU
			// Task - Prints the array of student IDs and test scores in columns
			// Returns - Nothing
	{
	int i,j;
	for(i=0; i<elementsUsed; ++i) //continue in this loop until there are no more values
	{
		// print the student id's and test scores on the same line with equal spacing
		outfile << setw(6) << IDs[i]<<" "; //print out the ID number
		for(j=0;j<=4;j++) // Loop through all columns of array
		outfile << setw(11)<< scores[i][j]; //print out the test score
		outfile << setw(10)<< letter[i]; //print out the letter grade
		outfile << endl;
	}
	return;
	}


	void SortArray(int IDs[], float scores[50][5], int &REU, int &CEU) // Sorts list high to low
			//Recieves - the ID numbers, test scores, rows used, and columns used
			//Task - sorts the parallel arrays based on ID number for high to low
			//Returns - the array with the average test score in the fourth index of the scores
	{
    int Row, N, col; //Initialize variables used for looping through both rows and columns
	float temp, temp2; //Initialize variables used for looping through both rows and columns
    for ( Row = 0 ; Row < REU-1; Row++) // Loop through every element in the array
     {
		 for ( N = REU-1; N > Row ; N--) // Test each element from the end to the beginning
		{
			if (IDs[N] > IDs[N-1] ) // Compare each element and swap if necessary
			{
				temp2 = IDs[N]; //place the index of the ID in a temporary variable
				IDs[N] = IDs[N-1]; // decrement the value of N for the ID number
				IDs[N-1] = temp2; //place the new index of ID number into the temp variable
				for(col=0; col<4; col++) //Loop through every column in each row
				{
				 temp = scores[N][col]; //place the index of the score in a temporary variable
				 scores[N][col] = scores[N-1][col]; // decrement the value of N for test scores
				 scores[N-1][col] = temp; //place the new index of scores into the temp variable
				}
			}
		}
     }
       return;  
 	} 


	void SortByAvg(int IDs[], float scores[50][5], char letter[], int &REU, int &CEU)//Sorts h to l
			//Recieves - the IDs, test scores, letter grades, rows used, and columns used
			//Task - sorts all three arrays based on average test score from high to low
			//Returns - the arrays sorted by average test score from high to low
	{
    int Row, N, col; //Initialize variables used for looping through both rows and columns
	float temp, temp2, temp3; 
    for ( Row = 0 ; Row < REU-1; Row++) // Loop through every element in the array
     {
		 for ( N = REU-1; N > Row ; N--) // Test each element from the end to the beginning
		{
			if (scores[N][4] > scores[N-1][4]) // Compare each element and swap if necessary
			{
				temp2 = IDs[N]; //place the index of the ID in a temporary variable
				temp3 = letter[N]; //place the index of the letter grade in a temp variable
				IDs[N] = IDs[N-1]; // decrement the value of N for the ID number
				letter[N] = letter[N-1]; //decrement the value of N for the letter grade
				IDs[N-1] = temp2; //place the new index of ID number into the temp variable
				letter[N-1] = temp3; //place the new index of the letter grade in a temp variable
				for(col=0; col<=4; col++) // Swap each column of elements accordingly
				{
				 temp = scores[N][col]; //place the index of the score in a temporary variable
				 scores[N][col] = scores[N-1][col]; // decrement the value of N for test scores
				 scores[N-1][col] = temp; //place the new index of scores into the temp variable
				}
			}
		}
     }
       return;  
 	} 


	void FindAvg(float scores[50][5], int &RU, int &CU)
			//Recieves - the test scores, rows used, and columns used
			//Task - computes the average for all the test scores in one row of the array
			//Returns - the array with the average test score in the fourth index of the scores
	{
	int row,col;
	float total;
	float avg;
	for(row = 0; row<RU; row++) //Loop through each row of the array of test scores
	{
		total = 0.0; //Zero the total for use in each row of the array
		for(col=0; col<CU; col++) //Loop through each column of the array of test scores
			total+=scores[row][col]; //Add the test score at each position to the total
			avg = total/CU; //Divide the total score by the columns used to find the average
			scores[row][4]=avg; //Add the average as the fourth element in the test score array
	}
	return;
	}

	void Letter(float testScores[50][5], char letter[], int &RU, int &CU)
			//Recieves - the test scores, letter grades, rows used, and columns used
			//Task - determines the letter grade for each ID number based on average test score
			//Returns - an array of letter grades parallel to each ID number and test scores
	{
	int row;
	for(row = 0; row<RU; row++)//Loop through each row of the array of test scores
	{
		if(testScores[row][4]>=90.0) //Print if the test score equals an A
			letter[row]='A';
		else if(testScores[row][4]>=80.0 && testScores[row][4]<90.0)//Print if score is a B
			letter[row]='B';
		else if(testScores[row][4]>=70.0 && testScores[row][4]<80.0)//Print if score is a C
			letter[row]='C';
		else if(testScores[row][4]>=60.0 && testScores[row][4]<70.0)//Print if score is a D
			letter[row]='D';
		else
			letter[row]='F'; //Print if score is an F
	}
	return;
	}


	void PrintOutput(ofstream &output, int &check)
{			//Recieves - the output file and an integer to see which columns will be printed out
			//Task - Prints the output preamble
			//Returns - nothing

	//Print out the column header
	output << setw(6)<<"Student ID"; 
	output << setw(9)<<"Test 1";
	output << setw(11)<<"Test 2";
	output << setw(11)<<"Test 3";
	output << setw(11)<<"Test 4"; 
	if(check==1) //Check to see if the Average and Grade have been calculated, and print if so
	{output << setw(11)<<"Average";
	output << setw(10)<<"Grade";
	}
	output << endl;
	output << setw(6)<<"----------";
	output << setw(9)<<"------";
	output << setw(11)<<"------";
	output << setw(11)<<"------";
	output << setw(11)<<"------";
	if(check==1) //Check to see if the Average and Grade have been calculated, and print if so
	{output << setw(11)<<"-------";
	output << setw(11)<<"------";
	}
	output << endl;
	}
