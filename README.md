# FSMA
Fixed-size multidimensional array containers for C++.

## Motivation

In C++, there are a number of dynamic multidimensional array and matrix libraries available such as boost::multi_array which allocate memory on the heap. However, sometimes we want statically sized compile time multidimensional arrays. On embedded systems with no free storethis may be our only option. Furthermore, libraries such as boost::multi_array permit an extensibility that is sometimes not required.

One option for allocating multidimensional arrays on the stack is to use built-in C multidimensional arrays. For example:
```C++
double arr[3][4];
```
However, built-in C multidimensional arrays have several problems:

1. They don't know their own size,
2. They provide no range checking. 3
3. They can't be passed cleanly. An array decays to a pointer to its first element at the "slightest provocation" as Mr. Stroustrup says.
4. They have no array operations such as assignment.
5. They don't interoperate well with the C++ Standard Library

This makes built-in C-style multidimensional arrays complicated and error-prone.

An alternative is to synthesize statically sized multidimensional arrays using boost::array/std::array. For example:
```C++
boost::array<boost::array<int, 5>, 5> example_array;
```
Disadvantages of this method are that begin(), end() and size() all return meaningless values for two or more dimensions. We cannot enumerate over the elements using begin() and end() because they will return pointers to the next dimension down and size() will only return the number of elements in the most significant dimension of the array. Consequently, we don't really gain any advantages over built-in multidimensional arrays using this method.

These factors and the syntax being somewhat clumsy make boost::array/std::array unsuitable for multidimensional arrays.

## Introduction
array_2d and array_3d are thin wrappers around built-in two-dimensional and three-dimensional arrays. They provide an STL-like interface to standard fixed-size multidimensional C arrays and are safer and have no worse performance than built-in C multidimensional arrays. The containers allow you to maintain information about the encapsulated array such as its size and dimensions, and you don't have to worry about nasty index arithmetic. They could be seen as analogous to boost::array/std::array for arrays of 2 and 3 dimensions.

## Usage

array_2d and array_3d can be conveniently initialized at declaration using an initializer list as they are aggregates.

## array_2d

Initialize a two-dimensional array with dimensions 3 x 5 (notice the extra pair ofbraces). Example:
```C++
#include "array_2d.hpp"

using namespace fsma;

array_2d<double, 3, 5> my2darray = {
{
 { 32.19, 47.29, 31.99, 19.11, 11.19},
 { 11.29, 22.49, 33.47, 17.29,  5.01 },
 { 41.97, 22.09,   9.76, 22.55,  6.22 }
}
};
```
Initialize all elements in multidimensional array to 0. Example:
```C++
array_2d<int, 3, 5> mytestarray = {0};
```
which is analogous to:
```C++
int builtin [3][5] = {0};
```
Initialize all elements to undefined values. Example:
```C++
array_2d<int, 3, 5> mytestarray2;
```
Assign a value to an element. Example:
```C++
my2darray(1,2) = 4; //non-range checked
my2darray.at(1,2) = 2;//range checked
```
Assign one value to all elements. Example:
```C++
mytestarray2.assign(226);//fills all elements of array with value 226. A synonym for fill() member function.
```
Get the total size of the array and the size of each dimension. Example:
```C++
cout << "Number of rows in array: " << my2darray.size_row() << '\n'; //synonym for .size_1d()
cout << "Number of columns in array: " << my2darray.size_col() << '\n'; // synonym for .size_2d()
cout << "Total size of the array: " << my2darray.size() << '\n';
```
Element access:
```C++
double k = my2darray(2, 3);//non-range checked
double q = my2darray.at(2,3);//range checked
```
Easy access to the first and last elements of the array using the familiar front() and back() member functions:
```C++
cout << "First element in array: " << my2darray.front() << '\n';
cout << "Last element in array: " << my2darray.back() << '\n';
```
Iterate array using subscript. Example:
```C++
for (int i = 0; i < my2darray.size_row(); ++i)
{
    for (int j = 0; j < my2darray.size_col(); ++j) {
    cout << my2darray(i, j) << ' ';//not range checked
    cout << my2darray.at(i,j) << ' ';//range checked

    if (j == my2darray.size_col() - 1) { //so we print the grid in correct visual form
        cout << '\n';
    }
  }
}
```

Iterate through the whole multidimensional array using an iterator. Example:
```C++
for (array_2d<double, 3, 5>::iterator it = my2darray.begin(); it != my2darray.end(); ++it)
    cout << *it << ' ';
```
Iterate through the whole multidimensional array in reverse using a reverse iterator. Example:
```C++
for (array_2d<double, 3, 5>::reverse_iterator it = my2darray.rbegin(); it != my2darray.rend(); ++it)
    cout << *it << ' ';
```

Extract data as a pointer to an array. Example:
```C++
double * array_2d_ptr = my2darray.data();
```
Copy initialization: 
```C++
array_2d <double, 3, 5> copyarray = my2darray;
```
Alternatively: 
```C++
array_2d <double, 3, 5> copyarray (my2darray);
```
Equality check:
```C++
if (my2darray == copyarray) {
    cout << "arrays are the same." << '\n';
}
else {
    cout << "arrays are different. << '\n';
}
```
sort array using STL sort algorithm:
```C++
std::sort(my2darray.begin(), my2darray.end());
```
The array can be passed safely and easily by reference. For example:
```C++
template <typename T, std::size_t D1, std::size_t D2>
void manipulate_multi(array_2d<T, D1, D2> & var);

int main(int argc, char * argv[])
{
array_2d<int,3, 3> game_board = {{

{7, 5, 8},

{3, 4, 7},

{4, 3, 6}

}};

manipulate_multi(game_board);

return 0;

}

template <typename T, std::size_t D1, std::size_t D2>
void manipulate_multi(array_2d<T, D1, D2> & var)
{
cout << var(2, 2) << endl;

/*...*/

}
```
## array_3d

The interface is very similar to array_2d.

Initialize a 3 dimensional array with dimensions 2 x 3 x 4 (Notice the extra pair of braces).Example:
```C++
array_3d <int,2,3,4> my3darray = {
{
{ {8,  28, 31, 90}, {41, 32, 39, 24}, {12, 23, 3, 4} },
{ {35, 21, 33, 4},  {1,  68, 33, 7},  {1, 2, 54, 83} }
}
};
```
Get the total size of the array and the size of each dimension. Example:
```C++
cout << "Size of first dimension: " << my3darray.size_1d() << endl;
cout << "Size of second dimension: " << my3darray.size_2d() << endl;
cout << "Size of third dimension: " << my3darray.size_3d() << endl;
cout << "Total size of the array: "<< my3daray.size() << endl;
```
## Issues

As array_2d and array_3d are aggregates, a default constructed array may contain uninitialized data. If T is a built-in type such a char, int or double, the value of each element will be undefined until assignment. However, if T is a user-defined type with a default constructor, all the elements in the array will be initialized by the default constructor.

## Future Developments

Support could be added for arrays of 4 dimensions or higher, although the use of multidimensional arrays with dimensions greater than 3 is uncommon.

It would be desirable to have a container which supports an arbitrary number of dimensions, This could be implemented using variadic templates introduced in C++11.

The begin() and end() member functions return iterators that represent the bounding range of the whole collection of elements. This makes array_2d and array_3d compatible with STL algorithms such as for_each(), irrespective of dimensionality. I implemented iterators this way as I believe it is important to be able to iterate through the entire range of elements in the array. Iterators could also be provided for each dimension in the future.

Further functionality could be implemented in future if desired. This could include member functions such as swap_rows() as well as support for slicing.

## Contact details

If you can spot any errors, suggest improvements to the implementation or want to request any new features please feel free to contact me at the email below:

ricky.65@hotmail.com

Copyright: Riccardo Marcangelo 2011 - 2017.
