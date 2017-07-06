---
title: An introduction to the `mex` file and useful implementations
categories: 
  - Dev
---

I use mostly Matlab to test my algorithms, it easy to implement and fast enough for matrix computation. However, for some computationally heavy tasks, I would normally turn to C++ for its efficiency. Thus I need an additional `mex` file so that my Matlab code can use the C++ code. To do so, most of the job required is to convert matlab data into C++ data, and I create some methods just for this purpose so that I don't need to do this all over again next time. So here it is.

## Basic knowledge
Here are a list of stuffs to know about mex fils
* gateway function: `mexfunction`;
* data type: `mxArray`
	* scope
	* create and destroy
	* ordering
	* access the data

### Gateway function
Just like every C/C++ program has a `main()` function, Matlab uses a gateway routine called `mexFunction`. Here is what it looks like
```matlab
void mexFunction(int nlhs, mxArray *plhs[],
		  int nrhs, mxArray *prhs[])
{
	// your code here
}
```

| Parameter | Description |
| --------- | ----------- |
| nlhs | Number of output (left-side) arguments, or the size of the plhs array. |
| plhs | Array of output arguments. |
| nrhs | Number of input (right-side) arguments, or the size of the prhs array. |
| prhs | aArray of input arguments. |

### All about `mxArray`
This section is an excerpt of the [MATLAB Data](https://www.mathworks.com/help/matlab/matlab_external/matlab-data.html) from Mathworks.

The Matlab works with one single object type: the Matlab array. All Matlab variables, including scalars, vectors, matrices, character arrays, cell arrays, structures, and objects, are stored in Matlab array. In C/C++, this Matlab array is declared to be of type `mxArray`. This `mxArray` structure contains the following information
* Its type
* Its dimension
* The data associated with this array
* If numeric, whether the variable is real or complex
* If structure or object, the number of fileds and field names
* If sparse, its indices and nonzero maximum elements

#### order of `mxArray`
The Matlab array follows the column-major order similar to Fortran while the C/C++ array follows row-major order.

#### create and destroy of `mxArray`
The `mexCreate*` function can create an `mxArray` while `mxCalloc` can allocate dynamic memory for array. Some useful ones are: `mxCreateDoubleMatrix`, `mxCreateNumericMatrix`

To deallocate memory, use either `mxDestroyArray` or `mxFree`

For more information, please go to [Create or Delete Array](https://www.mathworks.com/help/matlab/create-or-delete-array_btl2zvw-2.html).

```cpp
// create mxArray
double ** createMatrix(IN int rows, 
                       IN int cols) 
{
  int i;
  double **matrix;

  if(((matrix = (double **) malloc(sizeof(double *)*rows)) == NULL) ||
     ((matrix[0] = (double *) malloc(sizeof(double)*rows*cols)) == NULL))
    return NULL;
  
  for(i=1; i<rows; i++)
    matrix[i] = matrix[0] + i*cols;

  memset(matrix[0], 0, sizeof(double)*rows*cols);

  return matrix;
}

int ** createIntMatrix(IN int rows, 
                       IN int cols) 
{
  int i;
  int **matrix;

  if(((matrix = (int **) malloc(sizeof(int *)*rows)) == NULL) ||
     ((matrix[0] = (int *) malloc(sizeof(int)*rows*cols)) == NULL))
    return NULL;
  
  for(i=1; i<rows; i++)
    matrix[i] = matrix[0] + i*cols;

  memset(matrix[0], 0, sizeof(int)*rows*cols);

  return matrix;
}

// free mxArray
void freeMatrix(IN  double **matrix)
{
  free(*matrix);
  free(matrix);
}

void freeIntMatrix(IN  int **matrix)
{
  free(*matrix);
  free(matrix);
}
```

#### scope of `mxArray`
* The `mxArray` passed to a `mex` file through the `plhs` input parameter exists outside the scope of the `mex` file, so is the `mex` created for an output argument. Do not free memory of those variables.
* The `mex` file should destroy temporary arrays and free dynamically allocated memory.

#### access data of `mxArray`
The `mxGet*` routines get references to the data in an `mxArray`. Each function provides access to specific information in the `mxArray`. Some useful functions are `mxGetPr` (for type *double*), `mxGetData` (for type other than *double*), `mxGetM`, `mxGetN`, and `mxGetString`. Many of these functions have the corresponding `mxSet*` routines that allows to modify values in the array. 

For more information, please go to [Fill mxArray](https://www.mathworks.com/help/matlab/matlab_external/filling-an-mxarray.html).
```cpp
#define IN
#define OUT
double** getMxArray(IN const mxArray *mxArr,
					OUT int *rows,
					OUT int *cols)
{
	mwSize ndim;
	double *arrDataPr, **matrix;

	if((ndim = mxGetNumberofDimensions(mxArr)) != 2)
	{
		printf("input matrix has %d dimension, not 2\n", ndim);
		return NULL;
	}
	*rows = mxGetM(mxArr);
	*cols = mxGetN(mxArr);

	if(((matrix = (double**)malloc(sizeof(double*) * (*rows))) == NULL) ||
	   ((matrix[0] = (double*)malloc(sizeof(double) * (*rows) * (*cols))) == NULL))
   	{
   		return NULL;
   	}

   	for (i = 0; i < (*rows); ++i)
   	{
   		maxtrix[i] = matrix[0] + i * (*cols);
   	}

   	arrDataPr = mxGetPr(mxArr);
   	for (j = 0; j < (*cols); ++j)
   		for (i = 0; i < (*rows); ++i)
   			maxtrix[i][j] = *(arrDataPr++);

   	return matrix;
}

mxArray* putMxArray(double *matrix, int rows, int cols)
{
	mxArray *mxArr;
	mxArr = mxCreateDoubleMatrix(rows, cols, mxREAL);
	double *ptr;
  	ptr = mxGetPr(mxArr);

	for(mwSize j=0; j<cols; j++)
		for(mwSize i=0; i<rows; i++)
			*(ptr ++) = matrix[i*cols + j];

	return mxArr;
}
```